---
title: trim_start ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの trim_start () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4550fb07da37658ecf11a4eb04ecdf199d8ba989
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87339546"
---
# <a name="trim_start"></a>trim_start()

指定された正規表現の先頭の一致を削除します。

## <a name="syntax"></a>構文

`trim_start(`*regex* `,`*テキスト*`)`

## <a name="arguments"></a>引数

* *regex*:*テキスト*の先頭からトリミングされる文字列または[正規表現](re2.md)。  
* *text*: 文字列。

## <a name="returns"></a>戻り値

*テキスト*の先頭で見つかった*regex*と一致する文字列のトリミング後の*テキスト*です。

## <a name="example"></a>例

ステートメントベルは、 *string_to_trim*の先頭から*部分文字列*をトリムします。

```kusto
let string_to_trim = @"https://bing.com";
let substring = "https://";
print string_to_trim = string_to_trim,trimmed_string = trim_start(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|https://bing.com|bing.com|

次のステートメントは、文字列の先頭から単語以外のすべての文字を取り除きます。

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_start(@"[^\w]+",str)
```

|str|trimmed_str|
|---|---|
|-Te st1//$|Te st1//$|
|-Te st2//$|Te st2//$|
|-Te st3//$|Te st3//$|
|-Te st4//$|Te st4//$|
|-Te st5//$|Te st5//$|

 