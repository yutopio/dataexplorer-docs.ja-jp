---
title: 演算子の概要-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの演算子の概要について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/20/2020
ms.localizationpriority: high
ms.openlocfilehash: 810eba264c717d156f74b9958edecb712d58a4fd
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513201"
---
# <a name="summarize-operator"></a>summarize 演算子

入力テーブルの内容を集計したテーブルを生成します。

```kusto
Sales | summarize NumTransactions=count(), Total=sum(UnitPrice * NumUnits) by Fruit, StartOfMonth=startofmonth(SellDateTime)
```

販売トランザクションの数と、果物と販売月ごとの合計金額を含むテーブルを返します。
出力列には、トランザクションの数、トランザクションの価値、果物、およびトランザクションが記録された月の開始日の日時が表示されます。

```kusto
T | summarize count() by price_range=bin(price, 10.0)
```

各間隔 ([0,10.0]、[10.0,20.0] など) で価格を持つ項目の数を示すテーブル。 この例では、数の列と価格範囲の列があります。 他のすべての入力列は無視されます。

## <a name="syntax"></a>Syntax

*T* `| summarize` [[*列* `=` ] *集計* [ `,` ...]] [ `by` [*列* `=` ] *groupexpression* [ `,` ...]]

## <a name="arguments"></a>引数

* *Column:* 結果列の省略可能な名前。 既定値は式から派生した名前です。
* *集計:* 列名を引数として持つ、やなどの [集計関数](summarizeoperator.md#list-of-aggregation-functions) の呼び出し `count()` `avg()` 。 [集計関数のリスト](summarizeoperator.md#list-of-aggregation-functions)を参照してください。
* *Groupexpression:* 入力データを参照できるスカラー式。
  出力には、すべてのグループ式の個別の値と同じ数のレコードが含まれます。

> [!NOTE]
> 入力テーブルが空の場合、出力は *Groupexpression* が使用されているかどうかによって異なります。
>
> * *Groupexpression* が指定されていない場合、出力は単一の (空の) 行になります。
> * *Groupexpression* を指定した場合、出力には行が含まれません。

## <a name="returns"></a>戻り値

入力列は、`by` 式の同じ値を持つグループにまとめられます。 次に、指定された集計関数によってグループごとに計算が行われ、各グループに対応する行が生成されます。 結果には、`by` 列のほか、計算された各集計に対応する 1 つ以上の列も含まれます (一部の集計関数は複数の列を返します)。

結果には、値の個別の組み合わせ (ゼロ) と同じ数の行があり `by` ます。 グループキーが指定されていない場合、結果には1つのレコードが含まれます。

数値の範囲を集計するには、を使用して `bin()` 範囲を不連続値に減らします。

> [!NOTE]
> * 集計式とグループ化式の両方に任意の式を指定できますが、単純な列名を使用するか、 `bin()` を数値列に適用する方がより効率的です。
> * Datetime 列の自動時間単位ビンはサポートされなくなりました。 代わりに、明示的なビン分割を使用してください。 たとえば、`summarize by bin(timestamp, 1h)` のようにします。

## <a name="list-of-aggregation-functions"></a>集計関数の一覧

|関数|説明|
|--------|-----------|
|[any()](any-aggfunction.md)|グループの空でないランダムな値を返します|
|[anyif()](anyif-aggfunction.md)|グループに対して空でないランダムな値 (述語を含む) を返します。|
|[arg_max()](arg-max-aggfunction.md)|引数が最大化されている場合に1つ以上の式を返します|
|[arg_min()](arg-min-aggfunction.md)|引数が最小化されている場合に1つ以上の式を返します|
|[avg ()](avg-aggfunction.md)|グループ全体の平均値を返します|
|[avgif()](avgif-aggfunction.md)|グループ全体の平均値を返します (述語を含む)|
|[binary_all_and](binary-all-and-aggfunction.md)|グループのバイナリを使用して集計値を返します。 `AND`|
|[binary_all_or](binary-all-or-aggfunction.md)|グループのバイナリを使用して集計値を返します。 `OR`|
|[binary_all_xor](binary-all-xor-aggfunction.md)|グループのバイナリを使用して集計値を返します。 `XOR`|
|[buildschema()](buildschema-aggfunction.md)|入力のすべての値を制御する最小限のスキーマを返します。 `dynamic`|
|[count()](count-aggfunction.md)|グループの数を返します|
|[countif()](countif-aggfunction.md)|グループの述語を使用してカウントを返します。|
|[dcount()](dcount-aggfunction.md)|グループ要素の概数を返します。|
|[dcountif()](dcountif-aggfunction.md)|グループ要素の概数を返します (述語を含む)|
|[make_bag()](make-bag-aggfunction.md)|グループ内の動的な値のプロパティバッグを返します。|
|[make_bag_if()](make-bag-if-aggfunction.md)|グループ内の動的な値のプロパティバッグを返します (述語を含む)|
|[make_list()](makelist-aggfunction.md)|グループ内のすべての値の一覧を返します。|
|[make_list_if()](makelistif-aggfunction.md)|グループ内のすべての値の一覧を返します (述語を含む)|
|[make_list_with_nulls()](make-list-with-nulls-aggfunction.md)|Null 値を含む、グループ内のすべての値の一覧を返します。|
|[make_set()](makeset-aggfunction.md)|グループ内の個別の値のセットを返します。|
|[make_set_if()](makesetif-aggfunction.md)|グループ内の個別の値のセットを返します (述語を含む)|
|[max()](max-aggfunction.md)|グループ全体の最大値を返します|
|[maxif()](maxif-aggfunction.md)|グループ全体の最大値を返します (述語を含む)|
|[min()](min-aggfunction.md)|グループ全体の最小値を返します|
|[minif()](minif-aggfunction.md)|グループ全体の最小値を返します (述語を含む)|
|[パーセンタイル ()](percentiles-aggfunction.md)|グループのパーセンタイルの概数を返します|
|[percentiles_array ()](percentiles-aggfunction.md)|グループのパーセンタイル近似を返します。|
|[percentilesw()](percentiles-aggfunction.md)|グループの加重パーセンタイルの概数を返します|
|[percentilesw_array ()](percentiles-aggfunction.md)|グループの加重パーセンタイル近似を返します。|
|[stdev()](stdev-aggfunction.md)|グループ全体の標準偏差を返します|
|[stdevif()](stdevif-aggfunction.md)|グループ全体の標準偏差を返します (述語を含む)|
|[sum()](sum-aggfunction.md)|グループ内の要素の合計を返します。|
|[sumif()](sumif-aggfunction.md)|グループ内の要素の合計を返します (述語を含む)|
|[variance()](variance-aggfunction.md)|グループ間の分散を返します。|
|[varianceif()](varianceif-aggfunction.md)|グループ間の分散を返します (述語を含む)|

## <a name="aggregates-default-values"></a>既定値の集計

次の表は、集計の既定値をまとめたものです。

演算子       |既定値                         
---------------|------------------------------------
 `count()`, `countif()`, `dcount()`, `dcountif()`         |   0                            
 `make_bag()`, `make_bag_if()`, `make_list()`, `make_list_if()`, `make_set()`, `make_set_if()` |    空の動的配列 ([])          
 その他すべて          |   null                           

 Null 値を含むエンティティに対してこれらの集計を使用する場合、null 値は無視され、計算には含まれません (以下の例を参照)。

## <a name="examples"></a>例

:::image type="content" source="images/summarizeoperator/summarize-price-by-supplier.png" alt-text="果物とサプライヤー別の価格の集計":::

## <a name="example-unique-combination"></a>例: 一意の組み合わせ

`ActivityType`テーブルに含まれるとの一意の組み合わせを確認し `CompletionStatus` ます。 集計関数はありません。グループ化キーだけです。 出力には、これらの結果の列のみが表示されます。

```kusto
Activities | summarize by ActivityType, completionStatus
```

|`ActivityType`|`completionStatus`
|---|---
|`dancing`|`started`
|`singing`|`started`
|`dancing`|`abandoned`
|`singing`|`completed`

## <a name="example-minimum-and-maximum-timestamp"></a>例: タイムスタンプの最小値と最大値

アクティビティテーブル内のすべてのレコードの最小タイムスタンプと最大タイムスタンプを検索します。 group by 句はないため、以下のように出力には行が 1 つしかありません。

```kusto
Activities | summarize Min = min(Timestamp), Max = max(Timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`

## <a name="example-distinct-count"></a>例: Distinct count

大陸ごとに1行を作成し、アクティビティが発生した都市の数を示します。 "大陸" にはいくつかの値があるため、' by ' 句にはグループ化関数は必要ありません。

```kusto
Activities | summarize cities=dcount(city) by continent
```

|`cities`|`continent`
|---:|---
|`4290`|`Asia`|
|`3267`|`Europe`|
|`2673`|`North America`|


## <a name="example-histogram"></a>例: ヒストグラム

次の例では、各アクティビティの種類のヒストグラムを計算します。 に `Duration` は多くの値があるため、を使用して、 `bin` その値を10分間隔でグループ化します。

```kusto
Activities | summarize count() by ActivityType, length=bin(Duration, 10m)
```

|`count_`|`ActivityType`|`length`
|---:|---|---
|`354`| `dancing` | `0:00:00.000`
|`23`|`singing` | `0:00:00.000`
|`2717`|`dancing`|`0:10:00.000`
|`341`|`singing`|`0:10:00.000`
|`725`|`dancing`|`0:20:00.000`
|`2876`|`singing`|`0:20:00.000`
|...

**集計の既定値の例**

演算子の入力に `summarize` 少なくとも1つの空のグループキーがある場合、結果は空になります。

演算子の入力に `summarize` 空のグループ化キーがない場合、結果はで使用される集計の既定値になり `summarize` ます。

```kusto
datatable(x:long)[]
| summarize any(x), arg_max(x, x), arg_min(x, x), avg(x), buildschema(todynamic(tostring(x))), max(x), min(x), percentile(x, 55), hll(x) ,stdev(x), sum(x), sumif(x, x > 0), tdigest(x), variance(x)
```

|any_x|max_x|max_x_x|min_x|min_x_x|avg_x|schema_x|max_x1|min_x1|percentile_x_55|hll_x|stdev_x|sum_x|sumif_x|tdigest_x|variance_x|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|||||||||||||||||

```kusto
datatable(x:long)[]
| summarize  count(x), countif(x > 0) , dcount(x), dcountif(x, x > 0)
```

|count_x|countif_|dcount_x|dcountif_x|
|---|---|---|---|
|0|0|0|0|

```kusto
datatable(x:long)[]
| summarize  make_set(x), make_list(x)
```

|set_x|list_x|
|---|---|
|[]|[]|

集計では、null 以外のすべての値が集計され、計算に参加した値のみがカウントされます (null 値は考慮されません)。

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize sum(y), avg(y)
```

|sum_y|avg_y|
|---|---|
|5|5|

通常のカウントでは、null がカウントされます。 

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize count(y)
```

|count_y|
|---|
|2|

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize make_set(y), make_set(y)
```

|set_y|set_y1|
|---|---|
|[5.0]|[5.0]|
