---
title: ピボット プラグイン - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのピボット プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e4ec5a94483ade822280ee4a71106c214bb4b9a5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511103"
---
# <a name="pivot-plugin"></a>ピボットプラグイン

入力テーブルの 1 つの列の一意の値を出力テーブルの複数の列に変換してテーブルを回転し、最終的な出力で必要な残りの列値に対して必要な集計を実行します。

```kusto
T | evaluate pivot(PivotColumn)
```

**構文**

`T | evaluate pivot(`*ピボット列*`[, `*集計関数*`] [,`*列 1* `[,`*列 2* ..`]])`

**引数**

* *ピボット列*: 回転する列。 この列の各固有値は、出力テーブルの列になります。
* *集計関数*: (オプション) は、入力テーブル内の複数の行を出力テーブルの 1 つの行に集計します。 現在サポートされている関数`min()` `max()`: `any()` `sum()`、 `dcount()` `avg()`、 `stdev()` `variance()`、 `count()` 、 、 `count()`、 、 、 (既定値は ) 。
* *column1*、*列 2*、.: (オプション) 列名。 出力テーブルには、指定された各列ごとに追加の列が含まれます。 default : ピボット列と集計列以外のすべての列。

**戻り値**

ピボットは、指定された列 (*column1*、 *column2*、..) を持つ回転テーブルと、ピボット列のすべての一意の値を返します。 ピボット列の各セルには、集計関数の計算が含まれます。

**注**

プラグインの`pivot`出力スキーマはデータに基づいているため、クエリは任意の 2 つの実行に対して異なるスキーマを生成する可能性があります。 また、アンパックされた列を参照しているクエリが、いつでも 「壊れた」可能性があることを意味します。 このため、 - 自動化ジョブにこのプラグインを使用することはお勧めしません。

**使用例**

### <a name="pivot-by-a-column"></a>列によるピボット

イベントの種類と状態の各 'AL' で始まる、この状態でこの型のイベントの数をカウントします。

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
|強風|0|95|
|極端な寒さ/風の寒さ|0|10|
|強風|22|0|


### <a name="pivot-by-a-column-with-aggregation-function"></a>集計関数を持つ列でピボットします。

イベントタイプと'AR'で始まる各状態について、直接死亡の総数を表示します。

```kusto
StormEvents 
| where State startswith "AR" 
| project State, EventType, DeathsDirect 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect))
```

|EventType|アーカンソー|アリゾナ|
|---|---|---|
|大雨|1|0|
|雷雨風|1|0|
|雷|0|1|
|鉄砲水|0|6|
|強風|1|0|
|暖房|3|0|


### <a name="pivot-by-a-column-with-aggregation-function-and-a-single-additional-column"></a>集計関数と 1 つの追加列を持つ列でピボットします。

結果は前の例と同じです。

```kusto
StormEvents 
| where State startswith "AR" 
| project State, EventType, DeathsDirect 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect), EventType)
```

|EventType|アーカンソー|アリゾナ|
|---|---|---|
|大雨|1|0|
|雷雨風|1|0|
|雷|0|1|
|鉄砲水|0|6|
|強風|1|0|
|暖房|3|0|


### <a name="specify-the-pivoted-column-aggregation-function-and-multiple-additional-columns"></a>ピボット列、集計関数、および複数の追加列を指定します。

イベントの種類、ソース、状態ごとに、直接の死亡回数を合計します。

```kusto
StormEvents 
| where State startswith "AR" 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect), EventType, Source)
```

|EventType|source|アーカンソー|アリゾナ|
|---|---|---|---|
|大雨|非常事態担当マネージャー|1|0|
|雷雨風|非常事態担当マネージャー|1|0|
|雷|新聞|0|1|
|鉄砲水|訓練を受けた観測員|0|2|
|鉄砲水|放送メディア|0|3|
|鉄砲水|新聞|0|1|
|強風|法執行機関|1|0|
|暖房|新聞|3|0|