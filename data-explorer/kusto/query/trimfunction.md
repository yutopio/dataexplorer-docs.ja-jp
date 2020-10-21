---
title: trim ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの trim () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a5a8bf4884bf6c493c1b3b960fce64fe143ed52e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251900"
---
# <a name="trim"></a>trim()

指定した正規表現の先頭と末尾の一致をすべて削除します。

## <a name="syntax"></a>構文

`trim(`*regex* `,`*テキスト*`)`

## <a name="arguments"></a>引数

* *regex*:*テキスト*の先頭または末尾からトリミングされる文字列または[正規表現](re2.md)。  
* *text*: 文字列。

## <a name="returns"></a>戻り値

*テキスト*の先頭または末尾にある*regex*と一致したものをトリミングした後の*テキスト*。

## <a name="example"></a>例

ステートメントベルは、 *string_to_trim*の先頭と末尾から*部分文字列*をトリムします。

```kusto
let string_to_trim = @"--https://bing.com--";
let substring = "--";
print string_to_trim = string_to_trim, trimmed_string = trim(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|--https://bing.com--|https://bing.com|

次のステートメントは、文字列の先頭と末尾から単語以外のすべての文字をトリミングします。

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim(@"[^\w]+",str)
```

|str|trimmed_str|
|---|---|
|-Te st1//$|Te st1|
|-Te st2//$|Te st2|
|-Te st3//$|Te st3|
|-Te st4//$|Te st4|
|-Te st5//$|Te st5|


 