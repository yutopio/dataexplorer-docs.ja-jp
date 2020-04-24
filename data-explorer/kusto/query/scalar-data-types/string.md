---
title: 文字列データ型 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの文字列データ型について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8c040045ba7146cf56487ca3a8729372084d3bf2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509624"
---
# <a name="the-string-data-type"></a>文字列データ型

データ`string`型は、Unicode 文字列を表します。 (Kusto 文字列は UTF-8 でエンコードされ、デフォルトでは 1MB に制限されています。

## <a name="string-literals"></a>文字列リテラル

`string`データ型のリテラルをエンコードするには、いくつかの方法があります。

* 二重引用符で文字列を囲むことによって(`"`):`"This is a string literal. Single quote characters (') do not require escaping. Double quote characters (\") are escaped by a backslash (\\)"`
* 文字列を一重引用符で囲むことにより(`'`):`'Another string literal. Single quote characters (\') require escaping by a backslash (\\). Double quote characters (") do not require escaping.'`

上記の 2 つの表現では、バックスラッシュ`\`( ) はエスケープを示します。
囲む引用符文字、タブ文字 ( ) 、改行`\t`文字 (`\n`) 、およびそれ`\\`自体をエスケープするために使用されます。

逐語的な文字列リテラルもサポートされています。 この形式では、バックスラッシュ文字 (`\`) はエスケープ文字ではなく、それ自体を表します。

* 二重引用符で囲む`"`( ):`@"This is a verbatim string literal that ends with a backslash\"`
* 一重引用符で囲まれて`'`( ):`@'This is a verbatim string literal that ends with a backslash\'`

クエリ テキスト内の 2 つの文字列リテラルの間に何も含まれず、空白文字とコメントだけで区切られた場合は、自動的に連結されて新しい文字列リテラルが形成されます (置換ができないまで)。
たとえば、次の式はすべて`13`、

```kusto
print strlen("Hello"', '@"world!"); // Nothing between them

print strlen("Hello" ', ' @"world!"); // Separated by whitespace only

print strlen("Hello"
  // Comment
  ', '@"world!"); // Separated by whitespace and a comment
```

## <a name="examples"></a>例

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

見られるように、文字列が二重引用符(`"`) で囲まれている場合、単一引用符 (`'`) 文字はエスケープを必要とせず、その逆も同様です。 これにより、コンテキストに従って文字列を簡単に引用できます。

## <a name="obfuscated-string-literals"></a>難読化された文字列リテラル

システムは、テレメトリと分析の目的でクエリを追跡し、保存します。
たとえば、クエリ テキストがクラスター所有者に対して使用可能になる場合があります。 クエリ テキストにシークレット情報 (パスワードなど) が含まれている場合、秘密にしておく必要がある情報が漏洩する可能性があります。 これを防ぐために、クエリ作成者は特定の文字列リテラルを**難読化された文字列リテラル**としてマークします。
クエリ テキスト内のこのようなリテラルは、自動的に複数のスター (`*`) 文字に置き換えられるため、後で分析することはできません。

> [!IMPORTANT]
> 秘密情報を含むすべての文字列リテラルを難読化された文字列リテラルとしてマーク**することを強くお勧めします**。

難読化された文字列リテラルは、"通常" の文字列リテラルを取り、その前に a`h`または`H`文字を付加することによって形成できます。 次に例を示します。

```kusto
h'hello'
h@'world'
h"hello"
```

> [!NOTE]
> 多くの場合、文字列リテラルの一部だけがシークレットです。 このような場合、リテラルを秘密でない部分と秘密部分に分割し、秘密部分を難読化済みとしてマークする場合に非常に便利です。 次に例を示します。

```kusto
print x="https://contoso.blob.core.windows.net/container/blob.txt?"
  h'sv=2012-02-12&se=2013-04-13T0...'
```