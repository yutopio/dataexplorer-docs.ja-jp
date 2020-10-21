---
title: trim_end ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの trim_end () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6a764752f126408ceb48f1c4a1af5c74014b6eab
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251988"
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