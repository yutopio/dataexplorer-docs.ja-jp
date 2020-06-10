---
title: 具体化 ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの具体化 () 関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/06/2020
ms.openlocfilehash: f5ea896d4701aa5aec1b22c1ff20853aea18f065
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664944"
---
# <a name="materialize"></a>materialize()

他のサブクエリが部分的な結果を参照できるように、クエリの実行時にサブクエリの結果をキャッシュできます。
 
**構文**

`materialize(`*expression*`)`

**引数**

* *式*: クエリの実行中に評価およびキャッシュされる表形式の式。

**ヒント**

* オペランドに1回だけ実行できる相互サブクエリがある場合は、結合または共用体で具体化を使用します。 次の例を参照してください。

* また、join/union の分岐が必要な場合にも役立ちます。

* 具現化は、キャッシュされた結果に名前を指定した場合にのみ、let ステートメントで使用できます。

**注:**

* 具体化のキャッシュサイズは**5 GB**です。 
  この制限はクラスターノードごとに行われ、同時に実行されるすべてのクエリに共通です。
  クエリでが使用され `materialize()` ていて、それ以上のデータをキャッシュが保持できない場合、クエリはエラーで中止されます。

**使用例**

次の例では、を `materialize()` 使用してクエリのパフォーマンスを向上させる方法を示します。
式 `_detailed_data` は関数を使用して定義される `materialize()` ため、1回だけ計算されます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let _detailed_data = materialize(StormEvents | summarize Events=count() by State, EventType);
_detailed_data
| summarize TotalStateEvents=sum(Events) by State
| join (_detailed_data) on State
| extend EventPercentage = Events*100.0 / TotalStateEvents
| project State, EventType, EventPercentage, Events
| top 10 by EventPercentage
```

|State|EventType|EventPercentage|イベント|
|---|---|---|---|
|ハワイ WATERS|Waterspout|100|2|
|レイクオンタリオ|海上雷雨風|100|8|
|アラスカ州 GULF|Waterspout|100|4|
|大西洋北部|海上雷雨風|95.2127659574468|179|
|レイク ERIE|海上雷雨風|92.5925925925926|25|
|E 太平洋|Waterspout|90|9|
|レイクミシガン|海上雷雨風|85.1648351648352|155|
|レイク HURON|海上雷雨風|79.3650793650794|50|
|メキシコの GULF|海上雷雨風|71.7504332755633|414|
|ハワイ|高サーフィン|70.0218818380744|320|


次の例では、乱数のセットを生成し、を計算します。 
* セット内の個別の値の数 (Dcount)
* セット内の上位3つの値 
* セット内のこれらのすべての値の合計 
 
この操作は[バッチ](batches.md)と具体化を使用して行うことができます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let randomSet = 
    materialize(
        range x from 1 to 3000000 step 1
        | project value = rand(10000000));
randomSet | summarize Dcount=dcount(value);
randomSet | top 3 by value;
randomSet | summarize Sum=sum(value)
```

結果セット 1:  

|Update|
|---|
|2578351|

結果セット 2: 

|value|
|---|
|9999998|
|9999998|
|9999997|

結果セット 3: 

|SUM|
|---|
|15002960543563|
