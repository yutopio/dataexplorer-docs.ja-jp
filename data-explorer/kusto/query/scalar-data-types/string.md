---
title: 文字列データ型-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの文字列データ型について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e7c043a9b4d8b141d2dc45e88022e191e6483c35
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264812"
---
# <a name="the-string-data-type"></a>文字列データ型

`string`データ型は Unicode 文字列を表します。 Kusto 文字列は UTF-8 でエンコードされ、既定では 1 MB に制限されています。

## <a name="string-literals"></a>文字列リテラル

データ型のリテラルをエンコードするには、いくつかの方法があり `string` ます。

* 文字列を二重引用符 () で囲み `"` ます。`"This is a string literal. Single quote characters (') don't require escaping. Double quote characters (\") are escaped by a backslash (\\)"`
* 文字列を単一引用符 () で囲み `'` ます。`'Another string literal. Single quote characters (\') require escaping by a backslash (\\). Double quote characters (") do not require escaping.'`

上記の2つの表記では、円記号 ( `\` ) 文字はエスケープを示しています。
これは、囲み引用符文字、タブ文字 ( `\t` )、改行文字 ( `\n` )、およびそれ自体 () をエスケープするために使用され `\\` ます。

逐語的文字列リテラルもサポートされています。 この形式では、円記号 ( `\` ) は、エスケープ文字としてではなく、その文字自体を表します。

* 二重引用符 () で囲み `"` ます。`@"This is a verbatim string literal that ends with a backslash\"`
* 単一引用符 () で囲み `'` ます。`@'This is a verbatim string literal that ends with a backslash\'`

クエリ内の2つの文字列リテラルの間に何もない場合、または空白とコメントのみで区切られた場合は、自動的に結合して新しい文字列リテラルを形成します。
たとえば、次の式はすべて長さを生成し `13` ます。

```kusto
print strlen("Hello"', '@"world!"); // Nothing between them

print strlen("Hello" ', ' @"world!"); // Separated by whitespace only

print strlen("Hello"
  // Comment
  ', '@"world!"); // Separated by whitespace and a comment
```

## <a name="examples"></a>使用例

```kusto
// Simple string notation
print s1 = 'some string', s2 = "some other string"

// Strings that include single or double-quotes can be defined as follows
print s1 = 'string with " (double quotes)',
          s2 = "string with ' (single quotes)"

// Strings with '\' can be prefixed with '@' (as in c#)
print myPath1 = @'C:\Folder\filename.txt'

// Escaping using '\' notation
print s = '\\n.*(>|\'|=|\")[a-zA-Z0-9/+]{86}=='
```

ご覧のように、文字列が二重引用符 () で囲まれている場合、 `"` 単一引用符 ( `'` ) 文字はエスケープを必要とせず、もう1つの方法でもあります。 このメソッドを使用すると、コンテキストに従って文字列を簡単に引用符で囲むことができます。

## <a name="obfuscated-string-literals"></a>難読化文字列リテラル

システムは、クエリを追跡し、テレメトリと分析の目的でそれらを格納します。
たとえば、クエリテキストがクラスターの所有者に対して使用可能になっている可能性があります。 クエリテキストにパスワードなどの機密情報が含まれている場合は、プライベートに保持する必要がある情報が漏洩する可能性があります。 このようなリークが発生しないように、クエリ作成者は、特定の文字列リテラルを難読化された**文字列リテラル**としてマークすることができます。
クエリテキスト内のこのようなリテラルは、 `*` 後で分析するために使用できないように、自動的にいくつかのアスタリスク () 文字に置き換えられます。

> [!IMPORTANT]
> 機密情報を含むすべての文字列リテラルを、難読化された文字列リテラルとしてマークします。

難読化された文字列リテラルを作成するには、"regular" 文字列リテラルを使用し、 `h` `H` その前にまたは文字を前に付加します。 

次に例を示します。

```kusto
h'hello'
h@'world'
h"hello"
```

> [!NOTE]
> 多くの場合、文字列リテラルの一部だけがシークレットです。 そのような場合は、リテラルを非シークレット部分とシークレット部分に分割します。 次に、シークレット部分を難読化済みとしてマークするだけです。

次に例を示します。

```kusto
print x="https://contoso.blob.core.windows.net/container/blob.txt?"
  h'sv=2012-02-12&se=2013-04-13T0...'
```
