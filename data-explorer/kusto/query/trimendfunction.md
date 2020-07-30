---
title: trim_end ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの trim_end () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cab78680a3b996234724bc052d75959928520289
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87339852"
---
# <a name="trim_end"></a>trim_end()

指定した正規表現の末尾の一致を削除します。

## <a name="syntax"></a>構文

`trim_end(`*regex* `,`*テキスト*`)`

## <a name="arguments"></a>引数

* *regex*:*テキスト*の末尾から削除する文字列または[正規表現](re2.md)。  
* *text*: 文字列。

## <a name="returns"></a>戻り値

*テキスト*の終わりで見つかった*regex*と一致するものをトリミングした後の*テキスト*。

## <a name="example"></a>例

ステートメントベルは、 *string_to_trim*の末尾から*部分文字列*をトリムします。

```kusto
let string_to_trim = @"bing.com";
let substring = ".com";
print string_to_trim = string_to_trim,trimmed_string = trim_end(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|--------------|--------------|
|bing.com      |bing.          |

次のステートメントは、文字列の末尾から単語以外のすべての文字をトリミングします。

```kusto
print str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_end(@"[^\w]+",str)
```

|str          |trimmed_str|
|-------------|-----------|
|-Te st1//$|-Te st1  |
|-Te st2//$|-Te st2  |
|-Te st3//$|-Te st3  |
|-Te st4//$|-Te st4  |
|-Te st5//$|-Te st5  |