---
title: トップネストオペレータ - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで最上位の入れ子になった演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 296c36f4653bec971c71dc210008af7b0d98959a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505935"
---
# <a name="top-nested-operator"></a>top-nested 演算子

上位の結果が階層型で生成されます。各レベルは前のレベル値に基づくドリルダウンになります。 

```kusto
T | top-nested 3 of Location with others="Others" by sum(MachinesNumber), top-nested 4 of bin(Timestamp,5m) by sum(MachinesNumber)
```

これは、ダッシュボードの視覚化シナリオや、次のような質問に答える必要がある場合に便利です: "K1のトップN値を見つける(いくつかの集計を使用して)。.それぞれの K2 のトップ M 値を見つけます (別の集計を使用)。..."

**構文**

*T* `|` `with others=` `asc` | `desc``,` `=` `by` *N1* `of` *Expression1* *AggName1* *ConstExpr1* *Aggregation1* [ N1 ] 式1 [ ConstExpr1 ] [ [ AggName1 ] アグリゲーション1 ] [ `top-nested`

**引数**

上にネストされた各ルールについて、
* *N1*: 各階層レベルで返す上位値の数。 省略可能 (省略した場合、すべての個別の値が返されます)。
* *式1*: 上位の値を選択するための式。 通常は *、T*の列名か、そのような列のビン分割操作 (例えば)`bin()`です。 
* *ConstExpr1*: 指定した場合、該当するネスト レベルに対して、上位の値に含まれていない他の値の集計結果を保持する追加の行が追加されます。
* *集計 1*: [sum()](sum-aggfunction.md) [、count()](count-aggfunction.md) [、max()](max-aggfunction.md) [、min()](min-aggfunction.md) [、dcount()](dcountif-aggfunction.md) [、avg()](avg-aggfunction.md)、[パーセンタイル()、](percentiles-aggfunction.md)[パーセンティルー()](percentiles-aggfunction.md)、またはこれらの集約の任意のアルゲブリックの組み合わせのいずれかである可能性のある集計関数への呼び出し。
* `asc` または `desc` (既定値) は、実際には選択が範囲の "下限" と "上限" のどちらから行われるかを制御します。

**戻り値**

入力列を含む階層テーブルと、各列に対して、各要素の同じレベルの集計の結果を含む新しい列が作成されます。
列は入力列の順序と同じ順序で配置され、新しく作成される列は集計列に近い列になります。 各レコードには、以前のすべてのレベルで前の最上位のルールをすべて適用し、この出力に現在のレベルのルールを適用した後に、各値が選択される階層構造があります。
つまり、レベル i の上位 n 個の値が、レベル i - 1 の各値に対して計算されます。
 
**ヒント**

* *集計*結果に列の名前を変更する: T |マシン別の場所のトップネスト3フォーロケーション = 合計 (マシン番号) .

* 返されるレコードの数が非常に多い可能性があります。最大 *(N1*+1) \* (*N2*+1) \* .\* *(Nm*+1) (m はレベルの数 *、Ni*はレベル i の最上位カウント)。

* 集計は、上記の 1 つである集計関数を持つ数値列を受け取る必要があります。

* このオプション`with others=`を使用して、あるレベルで上位 N 値ではない他のすべての値の集計値を取得します。

* あるレベルを取得`with others=`する必要がない場合は、NULL 値が追加されます (アグリゲート列とレベルキーについては、以下の例を参照してください)。


* 次のような追加の top ネストされたステートメントを追加することで、選択した上位入れ子の候補の追加の列を返す可能性があります (以下の例を参照)。

```kusto
top-nested 2 of ...., ..., ..., top-nested of <additionalRequiredColumn1> by max(1), top-nested of <additionalRequiredColumn2> by max(1)
```

**例**

```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation by sum(BeginLat)
```

|State|aggregated_State|source|aggregated_Source|エンドロケーション|aggregated_EndLocation|
|---|---|---|---|---|---|
|カンザス|87771.2355000001|法執行機関|18744.823|FT スコット|264.858|
|カンザス|87771.2355000001|パブリック|22855.6206|バックリン|488.2457|
|カンザス|87771.2355000001|訓練を受けた観測員|21279.7083|シャロン SPGS|388.7404|
|テキサス州|123400.5101|パブリック|13650.9079|アマリロ|246.2598|
|テキサス州|123400.5101|法執行機関|37228.5966|ペリートン|289.3178|
|テキサス州|123400.5101|訓練を受けた観測員|13997.7124|クロード|421.44|


* 他の例を使用する場合:

```kusto
StormEvents
| top-nested 2 of State with others = "All Other States" by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation with others = "All Other End Locations" by  sum(BeginLat)


```

|State|aggregated_State|source|aggregated_Source|エンドロケーション|aggregated_EndLocation|
|---|---|---|---|---|---|
|カンザス|87771.2355000001|法執行機関|18744.823|FT スコット|264.858|
|カンザス|87771.2355000001|パブリック|22855.6206|バックリン|488.2457|
|カンザス|87771.2355000001|訓練を受けた観測員|21279.7083|シャロン SPGS|388.7404|
|テキサス州|123400.5101|パブリック|13650.9079|アマリロ|246.2598|
|テキサス州|123400.5101|法執行機関|37228.5966|ペリートン|289.3178|
|テキサス州|123400.5101|訓練を受けた観測員|13997.7124|クロード|421.44|
|カンザス|87771.2355000001|法執行機関|18744.823|その他の終端ロケーション|18479.965|
|カンザス|87771.2355000001|パブリック|22855.6206|その他の終端ロケーション|22367.3749|
|カンザス|87771.2355000001|訓練を受けた観測員|21279.7083|その他の終端ロケーション|20890.9679|
|テキサス州|123400.5101|パブリック|13650.9079|その他の終端ロケーション|13404.6481|
|テキサス州|123400.5101|法執行機関|37228.5966|その他の終端ロケーション|36939.2788|
|テキサス州|123400.5101|訓練を受けた観測員|13997.7124|その他の終端ロケーション|13576.2724|
|カンザス|87771.2355000001|||その他の終端ロケーション|24891.0836|
|テキサス州|123400.5101|||その他の終端ロケーション|58523.2932000001|
|その他すべての状態|1149279.5923|||その他の終端ロケーション|1149279.5923|


次のクエリは、上記の例で使用した最初のレベルと同じ結果を示しています。

```kusto
 StormEvents
 | where State !in ('TEXAS', 'KANSAS')
 | summarize sum(BeginLat)
```

|sum_BeginLat|
|---|
|1149279.5923|


上位入れ子の結果に別の列 (EventType) を要求する: 

```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 1 of EndLocation by sum(BeginLat), top-nested of EventType  by tmp = max(1)
| project-away tmp
```

|State|aggregated_State|source|aggregated_Source|エンドロケーション|aggregated_EndLocation|EventType|
|---|---|---|---|---|---|---|
|カンザス|87771.2355000001|訓練を受けた観測員|21279.7083|シャロン SPGS|388.7404|雷雨風|
|カンザス|87771.2355000001|訓練を受けた観測員|21279.7083|シャロン SPGS|388.7404|ひょう|
|カンザス|87771.2355000001|訓練を受けた観測員|21279.7083|シャロン SPGS|388.7404|竜巻|
|カンザス|87771.2355000001|パブリック|22855.6206|バックリン|488.2457|ひょう|
|カンザス|87771.2355000001|パブリック|22855.6206|バックリン|488.2457|雷雨風|
|カンザス|87771.2355000001|パブリック|22855.6206|バックリン|488.2457|洪水|
|テキサス州|123400.5101|訓練を受けた観測員|13997.7124|クロード|421.44|ひょう|
|テキサス州|123400.5101|法執行機関|37228.5966|ペリートン|289.3178|ひょう|
|テキサス州|123400.5101|法執行機関|37228.5966|ペリートン|289.3178|洪水|
|テキサス州|123400.5101|法執行機関|37228.5966|ペリートン|289.3178|鉄砲水|

最後のネストされたレベル (この例では EndLocation) で結果を並べ替え、このレベルの値ごとにインデックスの並べ替え順を指定するには (グループごと):

```kusto
StormEvents
| top-nested 2 of State  by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 4 of EndLocation by  sum(BeginLat)
| order by State , Source, aggregated_EndLocation
| summarize EndLocations = make_list(EndLocation, 10000) , endLocationSums = make_list(aggregated_EndLocation, 10000) by State, Source
| extend indicies = range(0, array_length(EndLocations) - 1, 1)
| mv-expand EndLocations, endLocationSums, indicies
```

|State|source|エンドロケーション|割り当て場所の合計|起訴|
|---|---|---|---|---|
|テキサス州|訓練を受けた観測員|クロード|421.44|0|
|テキサス州|訓練を受けた観測員|アマリロ|316.8892|1|
|テキサス州|訓練を受けた観測員|ダルハート|252.6186|2|
|テキサス州|訓練を受けた観測員|ペリートン|216.7826|3|
|テキサス州|法執行機関|ペリートン|289.3178|0|
|テキサス州|法執行機関|リーキー|267.9825|1|
|テキサス州|法執行機関|ブラケットビル|264.3483|2|
|テキサス州|法執行機関|ギルマー|261.9068|3|
|カンザス|訓練を受けた観測員|シャロン SPGS|388.7404|0|
|カンザス|訓練を受けた観測員|アトウッド|358.6136|1|
|カンザス|訓練を受けた観測員|レノア|317.0718|2|
|カンザス|訓練を受けた観測員|スコット シティ|307.84|3|
|カンザス|パブリック|バックリン|488.2457|0|
|カンザス|パブリック|アッシュランド|446.4218|1|
|カンザス|パブリック|保護|446.11|2|
|カンザス|パブリック|メアデ州立公園|371.1|3|