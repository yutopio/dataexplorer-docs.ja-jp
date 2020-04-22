---
title: 集計オペレーター - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの集計演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/20/2020
ms.openlocfilehash: ed1808f173d0f779c84f9405987d7395de833120
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663219"
---
# <a name="summarize-operator"></a>summarize 演算子

入力テーブルの内容を集計したテーブルを生成します。

```kusto
T | summarize count(), avg(price) by fruit, supplier
```

各サプライヤーからの各果物の数と平均価格を示す表。 果物とサプライヤーのそれぞれの異なる組み合わせの出力に行があります。 出力列には、カウント、平均価格、果物、サプライヤーが表示されます。 他のすべての入力列は無視されます。

```kusto
T | summarize count() by price_range=bin(price, 10.0)
```

各間隔 ([0,10.0]、[10.0,20.0] など) で価格を持つ項目の数を示すテーブル。 この例では、数の列と価格範囲の列があります。 他のすべての入力列は無視されます。

**構文**

*T* `| summarize` [[*列*`=`]`,`*集計*[ .]][`by` [*列*`=`]`,` *グループ式*[ .]]

**引数**

* *Column:* 結果列の省略可能な名前。 既定値は式から派生した名前です。
* *集約:* 列名を引数として使用する、 `count()` `avg()`または などの[集計関数](summarizeoperator.md#list-of-aggregation-functions)の呼び出し。 [集計関数のリスト](summarizeoperator.md#list-of-aggregation-functions)を参照してください。
* *GroupExpression:* 列に対する式です。個別の値のセットを示します。 通常は、限られた値のセットが既に指定されている列名か、引数として数値列または時間列が指定されている `bin()` になります。 

> [!NOTE]
> 入力テーブルが空の場合、出力は*GroupExpression*が使用されているかどうかによって異なります。
>
> * *GroupExpression*が指定されていない場合、出力は 1 つの (空の) 行になります。
> * *グループ式*が指定されている場合、出力には行がありません。

**戻り値**

入力列は、`by` 式の同じ値を持つグループにまとめられます。 次に、指定された集計関数によってグループごとに計算が行われ、各グループに対応する行が生成されます。 結果には、`by` 列のほか、計算された各集計に対応する 1 つ以上の列も含まれます (一部の集計関数は複数の列を返します)。

結果には、値の異なる組み合わせがある`by`(ゼロになる可能性があります) と同じ数の行が含まれます。 グループ キーが指定されていない場合、結果は 1 つのレコードになります。

数値の範囲を集計`bin()`するには、範囲を不連続値に減らします。

> [!NOTE]
> * 集計式とグループ化式の両方に任意の式を指定できますが、単純な列名を使用するか、 `bin()` を数値列に適用する方がより効率的です。
> * 日時列の自動時給ビンはサポートされなくなりました。 代わりに明示的なビンを使用してください。 たとえば、「 `summarize by bin(timestamp, 1h)` 」のように入力します。

## <a name="list-of-aggregation-functions"></a>集計関数の一覧

|機能|説明|
|--------|-----------|
|[any()](any-aggfunction.md)|グループの空でない値をランダムに返します。|
|[anyif()](anyif-aggfunction.md)|グループの空でない値をランダムに返します (述語付き)|
|[arg_max()](arg-max-aggfunction.md)|引数が最大化されたときに 1 つ以上の式を返します。|
|[arg_min()](arg-min-aggfunction.md)|引数が最小化されている場合に 1 つ以上の式を返します。|
|[avg()](avg-aggfunction.md)|グループ全体の平均値を返します。|
|[avgif()](avgif-aggfunction.md)|グループ全体の平均値を返します (述語付き)|
|[binary_all_and](binary-all-and-aggfunction.md)|グループのバイナリ`AND`を使用して集計値を返します。|
|[binary_all_or](binary-all-or-aggfunction.md)|グループのバイナリ`OR`を使用して集計値を返します。|
|[binary_all_xor](binary-all-xor-aggfunction.md)|グループのバイナリ`XOR`を使用して集計値を返します。|
|[buildschema()](buildschema-aggfunction.md)|入力のすべての値を受け入れた最小スキーマ`dynamic`を返します。|
|[count()](count-aggfunction.md)|グループのカウントを返します。|
|[countif()](countif-aggfunction.md)|グループの述語を持つカウントを返します。|
|[dcount()](dcount-aggfunction.md)|グループ要素のおおよその個別のカウントを返します。|
|[dcountif()](dcountif-aggfunction.md)|グループ要素の概算別のカウントを返します (述語付き)|
|[make_bag()](make-bag-aggfunction.md)|グループ内の動的な値のプロパティ バッグを返します。|
|[make_bag_if()](make-bag-if-aggfunction.md)|グループ内の動的な値のプロパティ バッグを返します (述語付き)|
|[make_list()](makelist-aggfunction.md)|グループ内のすべての値のリストを返します。|
|[make_list_if()](makelistif-aggfunction.md)|グループ内のすべての値のリストを返します (述語付き)|
|[make_list_with_nulls()](make-list-with-nulls-aggfunction.md)|グループ内のすべての値のリストを返します。(NULL 値を含む)|
|[make_set()](makeset-aggfunction.md)|グループ内の一連の個別値を返します。|
|[make_set_if()](makesetif-aggfunction.md)|グループ内の一連の個別値を返します (述語付き)|
|[max()](max-aggfunction.md)|グループ全体の最大値を返します。|
|[maxif()](maxif-aggfunction.md)|グループ全体の最大値を返します (述語付き)|
|[min()](min-aggfunction.md)|グループ全体の最小値を返します。|
|[minif()](minif-aggfunction.md)|グループ全体の最小値を返します (述語付き)|
|[percentiles()](percentiles-aggfunction.md)|グループの百分位概数を返します。|
|[percentiles_array()](percentiles-aggfunction.md)|グループの百分位数の近似値を返します。|
|[パーセンタイルw()](percentiles-aggfunction.md)|グループの重み付けされた百分位数近似値を返します。|
|[percentilesw_array()](percentiles-aggfunction.md)|グループの重み付けされた百分位数の近似値を返します。|
|[stdev()](stdev-aggfunction.md)|グループ全体の標準偏差を返します。|
|[stdevif()](stdevif-aggfunction.md)|グループ全体の標準偏差を返します (述語付き)|
|[sum()](sum-aggfunction.md)|グループを使用する要素の合計を返します。|
|[sumif()](sumif-aggfunction.md)|グループを持つ要素の合計を返します (述語付き)|
|[variance()](variance-aggfunction.md)|グループ全体の分散を返します。|
|[varianceif()](varianceif-aggfunction.md)|グループ全体の分散を返します (述語付き)|

## <a name="aggregates-default-values"></a>既定値の集計

次の表は、集計の既定値をまとめたものです。

演算子       |既定値                         
---------------|------------------------------------
 `count()`, `countif()`, `dcount()`, `dcountif()`         |   0                            
 `make_bag()`, `make_bag_if()`, `make_list()`, `make_list_if()`, `make_set()`, `make_set_if()` |    空の動的配列 ([])          
 その他すべて          |   null                           

 null 値を含むエンティティに対してこれらの集計を使用する場合、NULL 値は無視され、計算に参加しません (下記の例を参照)。

## <a name="examples"></a>例

![alt text](./Images/aggregations/01.png "01")

**例**

テーブル内の`ActivityType`一意の組`CompletionStatus`み合わせと、テーブル内に存在する一意の組み合わせを決定します。 集計関数はなく、グループ化キーだけです。 出力には、これらの結果の列が表示されます。

```kusto
Activities | summarize by ActivityType, completionStatus
```

|`ActivityType`|`completionStatus`
|---|---
|`dancing`|`started`
|`singing`|`started`
|`dancing`|`abandoned`
|`singing`|`completed`

**例**

[アクティビティ] テーブルのすべてのレコードのタイムスタンプの最小値と最大値を検索します。 group by 句はないため、以下のように出力には行が 1 つしかありません。

```kusto
Activities | summarize Min = min(Timestamp), Max = max(Timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`

**例**

各大陸の行を作成し、活動が発生する都市の数を示します。 "大陸" の値が少ないため、'by' 句にグループ化関数は必要ありません。

    Activities | summarize cities=dcount(city) by continent

|`cities`|`continent`
|---:|---
|`4290`|`Asia`|
|`3267`|`Europe`|
|`2673`|`North America`|


**例**

次の例では、アクティビティの種類ごとにヒストグラムを計算します。 値`Duration`は多いため、10 分間隔に値をグループ化するために使用`bin`します。

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

演算子の`summarize`入力に少なくとも 1 つの空の group-by キーがある場合、結果も空になります。

operator の`summarize`入力に空の group-by キーがない場合、結果は で使用される集計の既定値になります`summarize`。

```kusto
range x from 1 to 10 step 1
| where 1 == 2
| summarize any(x), arg_max(x, x), arg_min(x, x), avg(x), buildschema(todynamic(tostring(x))), max(x), min(x), percentile(x, 55), hll(x) ,stdev(x), sum(x), sumif(x, x > 0), tdigest(x), variance(x)
```

|any_x|max_x|max_x_x|min_x|min_x_x|avg_x|schema_x|max_x1|min_x1|percentile_x_55|hll_x|stdev_x|sum_x|sumif_x|tdigest_x|variance_x|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|||||||||||||||||

```kusto
range x from 1 to 10 step 1
| where 1 == 2
| summarize  count(x), countif(x > 0) , dcount(x), dcountif(x, x > 0)
```

|count_x|countif_|dcount_x|dcountif_x|
|---|---|---|---|
|0|0|0|0|

```kusto
range x from 1 to 10 step 1
| where 1 == 2
| summarize  make_set(x), make_list(x)
```

|set_x|list_x|
|---|---|
|[]|[]|

この集計は、すべての非 NULL を合計し、計算に参加した値のみをカウントします (NULL は考慮されません)。

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize sum(y), avg(y)
```

|sum_y|avg_y|
|---|---|
|5|5|

通常のカウントは null をカウントします。 

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
