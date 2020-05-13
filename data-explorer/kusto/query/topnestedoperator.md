---
title: 上位の入れ子になった演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの最上位の演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f87606442dcc5d7c9e7e0fceec379c37169757c3
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370769"
---
# <a name="top-nested-operator"></a>top-nested 演算子

上位の結果が階層型で生成されます。各レベルは前のレベル値に基づくドリルダウンになります。 

```kusto
T | top-nested 3 of Location with others="Others" by sum(MachinesNumber), top-nested 4 of bin(Timestamp,5m) by sum(MachinesNumber)
```

これは、ダッシュボードの視覚化のシナリオ、または "K1 の上位 N の値を検索する (一部の集計を使用する)" という質問に答える必要がある場合に便利です。それぞれについて、(別の集計を使用して) K2 のトップ-M 値を見つけます。..."

**構文**

*T* `|` `top-nested` [*N1*] `of` *Expression1* [ `with others=` *ConstExpr1*] `by` [*AggName1* `=` ] *Aggregation1* [ `asc`  |  `desc` ] [ `,` ...]

**引数**

最上位の入れ子になったルールごとに、次のようにします。
* *N1*: 階層レベルごとに返される上位の値の数。 省略可能 (省略した場合、すべての個別の値が返されます)。
* *Expression1*: top 値を選択する式。 通常は、 *T*の列名、またはその `bin()` ような列に対するビン分割操作 (など) のいずれかです。 
* *ConstExpr1*: 指定されている場合、該当する入れ子レベルでは、上位の値に含まれていない他の値の集計結果を保持する追加の行が追加されます。
* *Aggregation1*: [sum ()](sum-aggfunction.md)、 [count ()](count-aggfunction.md)、 [max ()](max-aggfunction.md)、 [min ()](min-aggfunction.md)、 [dcount ()](dcountif-aggfunction.md)、 [avg ()](avg-aggfunction.md)、[百分位 ()](percentiles-aggfunction.md)、 [percentilew (](percentiles-aggfunction.md))、またはこれらの集計の任意の algebric 組み合わせのいずれかである集計関数の呼び出し。
* `asc` または `desc` (既定値) は、実際には選択が範囲の "下限" と "上限" のどちらから行われるかを制御します。

**戻り値**

言及テーブルには、入力列と、各要素に対して同じレベルの集計結果を含む新しい列が生成されます。
列は入力列と同じ順序で配置され、新しく生成された列は集計列の近くに配置されます。 各レコードには言及構造があります。これは、前のレベルのすべての上位入れ子になったルールを適用してから、この出力に対する現在のレベルのルールを適用した後に、各値が選択されます。
これは、レベル i の上位 n の値が、レベル i-1 の各値に対して計算されることを意味します。
 
**ヒント**

* *集計*結果としてで列の名前を変更する: T |場所を指定した場所の上位3つの場所 (場所 = 合計 (個の場所)....

* 返されるレコードの数は非常に大きい場合があります。最大 (*N1*+ 1) \* (*N2*+ 1) \* ... \* (*Nm*+ 1) (m はレベルの数、 *Ni*は level i の最上位の数)。

* 集計関数を含む数値列を受け取る必要があります。集計関数は、前述のいずれかです。

* オプションを使用して、 `with others=` あるレベルの上位 N の値ではないその他すべての値の集計値を取得します。

* いくつかのレベルの取得に関心がない場合は `with others=` 、null 値が追加されます (集計列およびレベルキーについては、次の例を参照してください)。


* 次のような上位の入れ子になったステートメントを追加することで、選択した最上位の候補に対して追加の列を返すことができます (以下の例を参照してください)。

```kusto
top-nested 2 of ...., ..., ..., top-nested of <additionalRequiredColumn1> by max(1), top-nested of <additionalRequiredColumn2> by max(1)
```

**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation by sum(BeginLat)
```

|State|aggregated_State|source|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|カンザス|87771.2355000001|法執行機関|18744.823|FT SCOTT|264.858|
|カンザス|87771.2355000001|パブリック|22855.6206|BUCKLIN|488.2457|
|カンザス|87771.2355000001|訓練を受けた観測員|21279.7083|SHARON SPGS|388.7404|
|テキサス州|123400.5101|パブリック|13650.9079|AMARILLO|246.2598|
|テキサス州|123400.5101|法執行機関|37228.5966|PERRYTON|289.3178|
|テキサス州|123400.5101|訓練を受けた観測員|13997.7124|CLDE|421.44|


* その他の例:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State with others = "All Other States" by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation with others = "All Other End Locations" by  sum(BeginLat)


```

|State|aggregated_State|source|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|カンザス|87771.2355000001|法執行機関|18744.823|FT SCOTT|264.858|
|カンザス|87771.2355000001|パブリック|22855.6206|BUCKLIN|488.2457|
|カンザス|87771.2355000001|訓練を受けた観測員|21279.7083|SHARON SPGS|388.7404|
|テキサス州|123400.5101|パブリック|13650.9079|AMARILLO|246.2598|
|テキサス州|123400.5101|法執行機関|37228.5966|PERRYTON|289.3178|
|テキサス州|123400.5101|訓練を受けた観測員|13997.7124|CLDE|421.44|
|カンザス|87771.2355000001|法執行機関|18744.823|その他のすべてのエンドロケーション|18479.965|
|カンザス|87771.2355000001|パブリック|22855.6206|その他のすべてのエンドロケーション|22367.3749|
|カンザス|87771.2355000001|訓練を受けた観測員|21279.7083|その他のすべてのエンドロケーション|20890.9679|
|テキサス州|123400.5101|パブリック|13650.9079|その他のすべてのエンドロケーション|13404.6481|
|テキサス州|123400.5101|法執行機関|37228.5966|その他のすべてのエンドロケーション|36939.2788|
|テキサス州|123400.5101|訓練を受けた観測員|13997.7124|その他のすべてのエンドロケーション|13576.2724|
|カンザス|87771.2355000001|||その他のすべてのエンドロケーション|24891.0836|
|テキサス州|123400.5101|||その他のすべてのエンドロケーション|58523.2932000001|
|その他のすべての状態|1149279.5923|||その他のすべてのエンドロケーション|1149279.5923|


次のクエリは、上記の例で使用した最初のレベルと同じ結果を示しています。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
 StormEvents
 | where State !in ('TEXAS', 'KANSAS')
 | summarize sum(BeginLat)
```

|sum_BeginLat|
|---|
|1149279.5923|


上位の入れ子になった結果に別の列 (EventType) を要求します。 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 1 of EndLocation by sum(BeginLat), top-nested of EventType  by tmp = max(1)
| project-away tmp
```

|State|aggregated_State|source|aggregated_Source|EndLocation|aggregated_EndLocation|EventType|
|---|---|---|---|---|---|---|
|カンザス|87771.2355000001|訓練を受けた観測員|21279.7083|SHARON SPGS|388.7404|雷雨風|
|カンザス|87771.2355000001|訓練を受けた観測員|21279.7083|SHARON SPGS|388.7404|ひょう|
|カンザス|87771.2355000001|訓練を受けた観測員|21279.7083|SHARON SPGS|388.7404|Tornado|
|カンザス|87771.2355000001|パブリック|22855.6206|BUCKLIN|488.2457|ひょう|
|カンザス|87771.2355000001|パブリック|22855.6206|BUCKLIN|488.2457|雷雨風|
|カンザス|87771.2355000001|パブリック|22855.6206|BUCKLIN|488.2457|洪水|
|テキサス州|123400.5101|訓練を受けた観測員|13997.7124|CLDE|421.44|ひょう|
|テキサス州|123400.5101|法執行機関|37228.5966|PERRYTON|289.3178|ひょう|
|テキサス州|123400.5101|法執行機関|37228.5966|PERRYTON|289.3178|洪水|
|テキサス州|123400.5101|法執行機関|37228.5966|PERRYTON|289.3178|鉄砲水|

最後に入れ子になったレベル (この例では EndLocation) で結果を並べ替え、このレベルの各値に対してインデックスの並べ替え順序を指定するには (グループあたり):

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State  by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 4 of EndLocation by  sum(BeginLat)
| order by State , Source, aggregated_EndLocation
| summarize EndLocations = make_list(EndLocation, 10000) , endLocationSums = make_list(aggregated_EndLocation, 10000) by State, Source
| extend indicies = range(0, array_length(EndLocations) - 1, 1)
| mv-expand EndLocations, endLocationSums, indicies
```

|State|source|EndLocations|endLocationSums 合計|決まっ|
|---|---|---|---|---|
|テキサス州|訓練を受けた観測員|CLDE|421.44|0|
|テキサス州|訓練を受けた観測員|AMARILLO|316.8892|1|
|テキサス州|訓練を受けた観測員|DALHART|252.6186|2|
|テキサス州|訓練を受けた観測員|PERRYTON|216.7826|3|
|テキサス州|法執行機関|PERRYTON|289.3178|0|
|テキサス州|法執行機関|キーを最小にする|267.9825|1|
|テキサス州|法執行機関|BRACKETTVILLE|264.3483|2|
|テキサス州|法執行機関|GILMER|261.9068|3|
|カンザス|訓練を受けた観測員|SHARON SPGS|388.7404|0|
|カンザス|訓練を受けた観測員|ATWOOD|358.6136|1|
|カンザス|訓練を受けた観測員|LENORA|317.0718|2|
|カンザス|訓練を受けた観測員|SCOTT CITY|307.84|3|
|カンザス|パブリック|BUCKLIN|488.2457|0|
|カンザス|パブリック|ASHLAND|446.4218|1|
|カンザス|パブリック|PROTECTION|446.11|2|
|カンザス|パブリック|MEADE 状態パーク|371.1|3|
