---
title: ピボットプラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのピボットプラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4662b1bd9f68778cab1f799f564499e23add5812
ms.sourcegitcommit: 6a0bd5b84f9bd739510c6a75277dec3a9e851edd
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2020
ms.locfileid: "84788904"
---
# <a name="pivot-plugin"></a>ピボットプラグイン

入力テーブル内の1つの列の一意の値を出力テーブル内の複数の列に変換してテーブルを回転し、最終的な出力に必要なその他の列値に必要な集計を実行します。

```kusto
T | evaluate pivot(PivotColumn)
```

**構文**

`T | evaluate pivot(`*Pivotcolumn* `[, `*集計 ationfunction* `] [,`*column1* `[,`*column2* ...`]])`

**引数**

* *pivotcolumn*: 回転する列。 この列からの一意の値はそれぞれ、出力テーブルの列になります。
* *集計関数*: (省略可能) 入力テーブル内の複数の行を出力テーブルの単一の行に集計します。 現在サポートされている関数:、、、、、、、、、、 `min()` `max()` `any()` `sum()` `dcount()` `avg()` `stdev()` `variance()` `make_list()` `make_bag()` `make_set()` 、 `count()` (既定値は `count()` )。
* *column1*、 *column2*、...: (省略可能) 列名。 出力テーブルには、指定された列ごとに追加の列が含まれます。 既定値: ピボットされた列と集計列以外のすべての列。

**戻り値**

Pivot は、指定された列 (*column1*、 *column2*、...) とピボット列のすべての一意の値を含む、回転したテーブルを返します。 ピボットされた列の各セルには、集計関数の計算が含まれます。

**注:**

プラグインの出力スキーマ `pivot` はデータに基づいているため、クエリを実行すると、2つの実行に対して異なるスキーマが生成される可能性があります。 これは、アンパックされた列を参照しているクエリがいつでも "壊れている" 可能性があることも意味します。 この理由により、automation ジョブにこのプラグインを使用することは推奨されません。

**使用例**

### <a name="pivot-by-a-column"></a>列でピボットする

"AL" で始まる各 EventType と状態について、この種類のイベントの数をこの状態でカウントします。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| project State, EventType 
| where State startswith "AL" 
| where EventType has "Wind" 
| evaluate pivot(State)
```

|EventType|ALABAMA|アラスカ|
|---|---|---|
|雷雨風|352|1|
|高風|0|95|
|極端なコールド/風力 Chill|0|10|
|強風|22|0|


### <a name="pivot-by-a-column-with-aggregation-function"></a>集計関数を含む列でピボットします。

"AR" で始まる各 EventType と状態について、direct deaths の合計数を表示します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State startswith "AR" 
| project State, EventType, DeathsDirect 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect))
```

|EventType|アーカンソー|州|
|---|---|---|
|重い雨|1|0|
|雷雨風|1|0|
|ライトニング|0|1|
|鉄砲水|0|6|
|強風|1|0|
|暖房|3|0|


### <a name="pivot-by-a-column-with-aggregation-function-and-a-single-additional-column"></a>集計関数と1つの追加列を含む列でピボットします。

結果は前の例と同じです。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State startswith "AR" 
| project State, EventType, DeathsDirect 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect), EventType)
```

|EventType|アーカンソー|州|
|---|---|---|
|重い雨|1|0|
|雷雨風|1|0|
|ライトニング|0|1|
|鉄砲水|0|6|
|強風|1|0|
|暖房|3|0|


### <a name="specify-the-pivoted-column-aggregation-function-and-multiple-additional-columns"></a>ピボットされた列、集計関数、および複数の追加の列を指定します。

各イベントの種類 (source および state) について、direct deaths の数を合計します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State startswith "AR" 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect), EventType, Source)
```

|EventType|source|アーカンソー|州|
|---|---|---|---|
|重い雨|非常事態担当マネージャー|1|0|
|雷雨風|非常事態担当マネージャー|1|0|
|ライトニング|新聞|0|1|
|鉄砲水|訓練を受けた観測員|0|2|
|鉄砲水|メディアのブロードキャスト|0|3|
|鉄砲水|新聞|0|1|
|強風|法執行機関|1|0|
|暖房|新聞|3|0|
