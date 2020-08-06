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
ms.openlocfilehash: 85a59adc355c3d8855c34bcf97d29d3bd6eea4a1
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803133"
---
# <a name="top-nested-operator"></a>top-nested 演算子

階層的な集計と上位の値の選択を生成します。各レベルは前のレベルを改良したものです。

```kusto
T | top-nested 3 of Location with others="Others" by sum(MachinesNumber), top-nested 4 of bin(Timestamp,5m) by sum(MachinesNumber)
```

演算子は、 `top-nested` 入力として表形式のデータと1つ以上の集計句を受け取ります。
最初の集計句 (左端) は、これらのレコードに対する式の固有の値に従って、入力レコードをパーティションに細分化します。 次に、句は、レコードに対してこの式を最大化または最小化する特定の数のレコードを保持します。 次の集計句は、同様の関数を入れ子になった形式で適用します。 次の各句は、前の句によって生成されたパーティションに適用されます。 この処理は、すべての集計句に対して続行されます。

たとえば、演算子を使用すると、 `top-nested` 「国、販売員、販売量などの売上の数値を含むテーブルについて」という質問に答えることができます。売上の上位5国は何ですか。 各国の上位3人の販売員はどのようなものですか。」

## <a name="syntax"></a>構文

*T* `|` `top-nested` *TopNestedClause2* [ `,` *TopNestedClause2*...]

ここで、 *Topnestedclause*の構文は次のとおりです。

[*N*] `of` [ *`ExprName`* `=` ] *`Expr`* [ `with` `others` `=` *`ConstExpr`* `by` *`AggName`* `=` *`Aggregation`* `asc`  |  `desc` ] [] []

## <a name="arguments"></a>引数

各*Topnestedclause*の場合:

* *`N`*: `long` この階層レベルで返される上位の値の数を示す型のリテラル。
  省略した場合、すべての個別の値が返されます。

* *`ExprName`*: 指定した場合、の値に対応する出力列の名前を設定 *`Expr`* します。

* *`Expr`*: この階層レベルで返される値を示す入力レコードの式。
  通常は、表形式の入力 (*T*)、またはそのような列に対する一部の計算 (など) の列参照です `bin()` 。

* *`ConstExpr`*: 指定した場合、階層レベルごとに1つのレコードが追加されます。この値には、"最上位にしない" すべてのレコードの集計値が含まれます。

* *`AggName`*: 指定されている場合、この識別子は、*集計*の値の出力の列名を設定します。

* *`Aggregation`*: 同じ値を共有するすべてのレコードに適用する集計を示す数値式 *`Expr`* 。 この集計の値によって、結果として得られるレコードのどれが "top" であるかが決まります。
  
  次の集計関数がサポートされています。
   * [sum ()](sum-aggfunction.md)、
   * [count ()](count-aggfunction.md)、
   * [max ()](max-aggfunction.md)、
   * [min ()](min-aggfunction.md)、
   * [dcount ()](dcountif-aggfunction.md)、
   * [avg ()](avg-aggfunction.md)、
   * [百分位 ()](percentiles-aggfunction.md)、および
   * [percentilew ()](percentiles-aggfunction.md)。 集計の代数的な組み合わせもサポートされています。

* `asc`または `desc` (既定値) は、集計値の範囲の "下" または "上" から選択されているかどうかを制御するように表示される場合があります。

## <a name="returns"></a>戻り値

この演算子は、各集計句に2つの列を持つテーブルを返します。

* 1つの列に句の計算の個別の値が保持 *`Expr`* されます (指定した場合は、列名が*exprname*になります)。

* 1つの列に集計計算の結果が格納されます (指定されている場合は、列名が*Aggregation* *ationname*になります)。

## <a name="notes"></a>Notes

値として指定されていない入力列は出力されません *`Expr`* 。
特定のレベルのすべての値を取得するには、次のような集計カウントを追加します。

* *N*の値を省略します
* の値として列名を使用します。*`Expr`*
* を `Ignore=max(1)` 集計として使用し、列を無視 (またはプロジェクトから) し `Ignore` ます。

レコードの数は、集計句の数 ((N1 + 1) \* (N2 + 1) \* ...) を使用して指数関数的に増加することがあります。*N*制限が指定されていない場合、レコードの増加はさらに高速になります。 このオペレーターが大量のリソースを消費する可能性があることを考慮してください。

集計の分布が非常に均一でない場合は、( *N*を使用して) 返される個別の値の数を制限し、ConstExpr オプションを使用して、 `with others=` *ConstExpr*その他のすべてのケースの "重み" を示す値を取得します。

## <a name="examples"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation by sum(BeginLat)
```

|State|aggregated_State|ソース|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|カンザス|87771.2355000001|法執行機関|18744.823|FT SCOTT|264.858|
|カンザス|87771.2355000001|パブリック|22855.6206|BUCKLIN|488.2457|
|カンザス|87771.2355000001|訓練を受けた観測員|21279.7083|SHARON SPGS|388.7404|
|テキサス州|123400.5101|パブリック|13650.9079|AMARILLO|246.2598|
|テキサス州|123400.5101|法執行機関|37228.5966|PERRYTON|289.3178|
|テキサス州|123400.5101|訓練を受けた観測員|13997.7124|CLDE|421.44|

オプション ' with 他 ' を使用します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State with others = "All Other States" by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation with others = "All Other End Locations" by  sum(BeginLat)


```

|State|aggregated_State|ソース|aggregated_Source|EndLocation|aggregated_EndLocation|
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

|State|aggregated_State|ソース|aggregated_Source|EndLocation|aggregated_EndLocation|EventType|
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

このレベルの各値 (グループごと) のインデックスの並べ替え順序を指定して、最後に入れ子になったレベル (この例では EndLocation) で結果を並べ替えます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State  by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 4 of EndLocation by  sum(BeginLat)
| order by State , Source, aggregated_EndLocation
| summarize EndLocations = make_list(EndLocation, 10000) , endLocationSums = make_list(aggregated_EndLocation, 10000) by State, Source
| extend indicies = range(0, array_length(EndLocations) - 1, 1)
| mv-expand EndLocations, endLocationSums, indicies
```

|State|ソース|EndLocations|endLocationSums 合計|連想|
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
