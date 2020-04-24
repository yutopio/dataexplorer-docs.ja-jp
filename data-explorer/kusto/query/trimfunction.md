---
title: トリム() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで trim() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aef2a61e5ac13fe9af9d8bc0dd130f3d085a3604
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505595"
---
# <a name="trim"></a>trim()

指定した正規表現の先頭と末尾の一致をすべて削除します。

**構文**

`trim(`*正規表現*`,`*テキスト*`)`

**引数**

* *regex*: 文字列または[正規表現](re2.md)を*テキスト*の先頭または末尾からトリミングします。  
* *text*: 文字列。

**戻り値**

*テキスト*の先頭および/または末尾に見つかった*正規表現*の一致をトリミングした後の*テキスト*。

**例**

ステートメントベローズは *、string_to_trim*の先頭と末尾から*部分文字列*をトリミングします。

```kusto
let string_to_trim = @"--https://bing.com--";
let substring = "--";
print string_to_trim = string_to_trim, trimmed_string = trim(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|--https://bing.com--|https://bing.com|

Next ステートメントは、文字列の先頭と末尾からすべての単語以外の文字をトリミングします。

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim(@"[^\w]+",str)
```

|str|trimmed_str|
|---|---|
|- テ st1// $|テ st1|
|- テ st2// $|テ st2|
|- テ st3// $|テ st3|
|- テ st4// $|テ st4|
|- テ st5// $|テ st5|


 