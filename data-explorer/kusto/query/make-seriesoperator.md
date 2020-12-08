---
title: make-series 演算子 - Azure Data Explorer
description: この記事では、Azure Data Explorer の make-series 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.localizationpriority: high
ms.openlocfilehash: 6357afeb0a5673584e27b84a231e3c65f897b8fc
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512368"
---
# <a name="make-series-operator"></a>make-series 演算子

指定された軸に沿って、指定された集計値の系列を作成します。

```kusto
T | make-series sum(amount) default=0, avg(price) default=0 on timestamp from datetime(2016-01-01) to datetime(2016-01-10) step 1d by fruit, supplier
```

## <a name="syntax"></a>構文

*T* `| make-series` [*MakeSeriesParamters*] [*Column* `=`] *Aggregation* [`default` `=` *DefaultValue*] [`,` ...] `on` *AxisColumn* [`from` *start*] [`to` *end*] `step` *step* [`by` [*Column* `=`] *GroupExpression* [`,` ...]]

## <a name="arguments"></a>引数

* *Column:* 結果列の省略可能な名前。 既定値は式から派生した名前です。
* *DefaultValue:* 値が存在しない場合に使用される既定値。 *AxisColumn* と *GroupExpression* の特定の値を持つ行がない場合、結果では、配列の対応する要素に *DefaultValue* が割り当てられます。 *DefaultValue* を省略した場合は 0 が想定されます。 
* *集計:* 引数として列名が指定された `count()` や `avg()` などの[集計関数](make-seriesoperator.md#list-of-aggregation-functions)の呼び出し。 [集計関数のリスト](make-seriesoperator.md#list-of-aggregation-functions)を参照してください。 `make-series` 演算子と共に使用できるのは、数値の結果を返す集計関数のみです。
* *AxisColumn:* 系列が並べ替えられる列。 これはタイムラインと考えることができますが、`datetime` 以外の任意の数値型も指定できます。
* *start*: (省略可能) 構築する各系列の *AxisColumn* の下限値。 *start*、*end*、および *step* は、指定された範囲内で、指定した *step* を使用して、*AxisColumn* 値の配列を作成するために使用されます。 すべての *Aggregation* 値は、この配列にそれぞれ並べ替えられます。 この *AxisColumn* 配列は、*AxisColumn* と同じ名前を持つ出力の最後の出力列でもあります。 *start* 値が指定されていない場合、start は各系列のデータを持つ最初のビン (ステップ) です。
* *end*: (省略可能) *AxisColumn* の上限値 (非包含)。 時系列の最後のインデックスはこの値よりも小さくなります (*start* に *step* の整数倍を加えた、*end* 未満の値です)。 *end* 値が指定されていない場合、各系列のデータを持つ最後のビン (ステップ) の上限になります。
* *step*:*AxisColumn* 配列の 2 つの連続する要素間の差 (つまり、ビンのサイズ)。
* *GroupExpression:* 個別の値のセットを示す、列に対する式。 通常は、限られた値のセットが既に指定されている列名です。 
* *MakeSeriesParameters*:動作を制御する、"*名前* `=` *値*" という形式の 0 個以上の (スペースで区切られた) パラメーター。 サポートされているパラメーターは次のとおりです。 
  
  |名前           |値                                        |[説明]                                                                                        |
  |---------------|-------------------------------------|------------------------------------------------------------------------------|
  |`kind`          |`nonempty`                               |make-series 演算子の入力が空の場合に既定の結果が生成されます。|                                

## <a name="returns"></a>戻り値

入力行は、`by` 式と `bin_at(`*AxisColumn*`, `*step*`, `*start*`)` 式の同じ値を持つグループにまとめられます。 次に、指定された集計関数によってグループごとに計算が行われ、各グループに対応する行が生成されます。 結果には、`by` 列、*AxisColumn* 列のほか、計算された各集計に対応する 1 つ以上の列も含まれます (複数の列または数値以外の結果はサポートされてない集計)。

この中間結果には、`by` と `bin_at(`*AxisColumn*`, `*step*`, `*start* `)` 値の個別の組み合わせと同じ数の行が含まれています。

最後に、中間結果の行は `by` 式と同じ値を持つグループにまとめられ、すべての集計値は配列 (`dynamic` 型の値) にまとめられます。 集計ごとに、同じ名前の配列を含む 1 つの列が存在します。 すべての *AxisColumn* 値を持つ範囲関数の出力の最後の列です。 この値はすべての行に対して繰り返されます。 

欠落しているビンには既定値が埋められるため、結果のピボット テーブルは、すべての系列でビン数 (つまり、集計値) が同じになります。  

**注:**

集計式とグループ化式の両方に任意の式を指定できますが、単純な列名を使用する方がより効率的です。

**代替構文**

*T* `| make-series` [*Column* `=`] *Aggregation* [`default` `=` *DefaultValue*] [`,` ...] `on` *AxisColumn* `in` `range(`*start*`,` *stop*`,` *step*`)` [`by` [*Column* `=`] *GroupExpression* [`,` ...]]

代替構文から生成される系列は、次の 2 つの点でメイン構文と異なります。
* *stop* 値は包含値です。
* インデックス軸のビン分割は、bin_at() ではなく bin() を使用して生成されます。つまり、*start* は生成された系列に含まれない可能性があります。

代替構文ではなく、make-series のメイン構文を使用することをお勧めします。

**Distribution と Shuffle**

`make-series` は、構文 hint.shufflekey を使用した `summarize` [shufflekey ヒント](shufflequery.md)をサポートしています。

## <a name="list-of-aggregation-functions"></a>集計関数の一覧

|機能|[説明]|
|--------|-----------|
|[any()](any-aggfunction.md)|グループの空でないランダム値を返します|
|[avg()](avg-aggfunction.md)|グループ全体の平均値を返します|
|[avgif()](avgif-aggfunction.md)|グループの述語を使用して平均を返します|
|[count()](count-aggfunction.md)|グループの数を返します|
|[countif()](countif-aggfunction.md)|グループの述語を使用してカウントを返します|
|[dcount()](dcount-aggfunction.md)|グループ要素の個別の概数を返します|
|[dcountif()](dcountif-aggfunction.md)|グループの述語を使用して個別の概数を返します|
|[max()](max-aggfunction.md)|グループ全体の最大値を返します|
|[maxif()](maxif-aggfunction.md)|グループの述語を使用して最大値を返します|
|[min()](min-aggfunction.md)|グループ全体の最小値を返します|
|[minif()](minif-aggfunction.md)|グループの述語を使用して最小値を返します|
|[stdev()](stdev-aggfunction.md)|グループ全体の標準偏差を返します|
|[sum()](sum-aggfunction.md)|グループ内の要素の合計を返します|
|[sumif()](sumif-aggfunction.md)|グループの述語を使用して要素の合計を返します|
|[variance()](variance-aggfunction.md)|グループ全体の分散を返します|

## <a name="list-of-series-analysis-functions"></a>系列分析関数の一覧

|機能|[説明]|
|--------|-----------|
|[series_fir()](series-firfunction.md)|[有限インパルス応答](https://en.wikipedia.org/wiki/Finite_impulse_response)フィルターを適用します|
|[series_iir()](series-iirfunction.md)|[無限インパルス応答](https://en.wikipedia.org/wiki/Infinite_impulse_response)フィルターを適用します|
|[series_fit_line()](series-fit-linefunction.md)|入力に最も近い直線を検索します|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|入力に最も近い線を検索し、動的オブジェクトを返します|
|[series_fit_2lines()](series-fit-2linesfunction.md)|入力に最も近い 2 本の線を検索します|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|入力に最も近い 2 本の線を検索し、動的オブジェクトを返します|
|[series_outliers()](series-outliersfunction.md)|系列内の異常な点にスコアを付けます|
|[series_periods_detect()](series-periods-detectfunction.md)|時系列に存在する最も重要な期間を検索します|
|[series_periods_validate()](series-periods-validatefunction.md)|時系列に特定の長さの周期的なパターンが含まれているかどうかを確認します|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|一般的な統計情報 (最小、最大、分散、標準偏差、平均) を含む複数の列を返します|
|[series_stats()](series-statsfunction.md)|一般的な統計情報 (最小、最大、分散、標準偏差、平均) を含む動的な値を生成します|
  
## <a name="list-of-series-interpolation-functions"></a>系列補間関数の一覧

|機能|[説明]|
|--------|-----------|
|[series_fill_backward()](series-fill-backwardfunction.md)|系列内の欠損値の後方埋め込み補間を実行します|
|[series_fill_const()](series-fill-constfunction.md)|系列の欠損値を指定した定数値に置き換えます|
|[series_fill_forward()](series-fill-forwardfunction.md)|系列内の欠損値の前方埋め込み補間を実行します|
|[series_fill_linear()](series-fill-linearfunction.md)|系列内の欠損値の線形補間を実行します|

* 注:補間関数では、既定で欠損値として `null` が想定されています。 そのため、系列に補間関数を使用する場合は、`make-series` に `default=`*double*(`null`) を指定してください。 

## <a name="example"></a>例
  
 指定した範囲のタイムスタンプで並べ替えられた、各サプライヤーからの各果物の数と平均価格の配列を示すテーブル。 出力には、果物とサプライヤーの個別の組み合わせごとに行があります。 出力列には、果物、サプライヤー、配列 (カウント、平均、およびタイムライン全体 (2016-01-01 から 2016-01-10 まで)) が表示されます。 すべての配列はそれぞれのタイムスタンプで並べ替えられ、すべての欠落値に既定値 (この例では 0) が埋められます。 他のすべての入力列は無視されます。
  
```kusto
T | make-series PriceAvg=avg(Price) default=0
on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

:::image type="content" source="images/make-seriesoperator/makeseries.png" alt-text="3 つのテーブル。1 つ目は生データを一覧表示するもの、2 つ目はサプライヤー、果物、日付の個別の組み合わせのみを含むもの、3 つ目は make-series の結果を含むものです。":::  

<!-- csl: https://help.kusto.windows.net:443/Samples --> 
```kusto
let data=datatable(timestamp:datetime, metric: real)
[
  datetime(2016-12-31T06:00), 50,
  datetime(2017-01-01), 4,
  datetime(2017-01-02), 3,
  datetime(2017-01-03), 4,
  datetime(2017-01-03T03:00), 6,
  datetime(2017-01-05), 8,
  datetime(2017-01-05T13:40), 13,
  datetime(2017-01-06), 4,
  datetime(2017-01-07), 3,
  datetime(2017-01-08), 8,
  datetime(2017-01-08T21:00), 8,
  datetime(2017-01-09), 2,
  datetime(2017-01-09T12:00), 11,
  datetime(2017-01-10T05:00), 5,
];
let interval = 1d;
let stime = datetime(2017-01-01);
let etime = datetime(2017-01-10);
data
| make-series avg(metric) on timestamp from stime to etime step interval 
```
  
|avg_metric|タイムスタンプ|
|---|---|
|[ 4.0, 3.0, 5.0, 0.0, 10.5, 4.0, 3.0, 8.0, 6.5 ]|[ "2017-01-01T00:00:00.0000000Z", "2017-01-02T00:00:00.0000000Z", "2017-01-03T00:00:00.0000000Z", "2017-01-04T00:00:00.0000000Z", "2017-01-05T00:00:00.0000000Z", "2017-01-06T00:00:00.0000000Z", "2017-01-07T00:00:00.0000000Z", "2017-01-08T00:00:00.0000000Z", "2017-01-09T00:00:00.0000000Z" ]|  


`make-series` への入力が空の場合、`make-series` の既定の動作でも空の結果が生成されます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let data=datatable(timestamp:datetime, metric: real)
[
  datetime(2016-12-31T06:00), 50,
  datetime(2017-01-01), 4,
  datetime(2017-01-02), 3,
  datetime(2017-01-03), 4,
  datetime(2017-01-03T03:00), 6,
  datetime(2017-01-05), 8,
  datetime(2017-01-05T13:40), 13,
  datetime(2017-01-06), 4,
  datetime(2017-01-07), 3,
  datetime(2017-01-08), 8,
  datetime(2017-01-08T21:00), 8,
  datetime(2017-01-09), 2,
  datetime(2017-01-09T12:00), 11,
  datetime(2017-01-10T05:00), 5,
];
let interval = 1d;
let stime = datetime(2017-01-01);
let etime = datetime(2017-01-10);
data
| limit 0
| make-series avg(metric) default=1.0 on timestamp from stime to etime step interval 
| count 
```

|Count|
|---|
|0|


`make-series` で `kind=nonempty` を使用すると、既定値の空でない結果が生成されます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let data=datatable(timestamp:datetime, metric: real)
[
  datetime(2016-12-31T06:00), 50,
  datetime(2017-01-01), 4,
  datetime(2017-01-02), 3,
  datetime(2017-01-03), 4,
  datetime(2017-01-03T03:00), 6,
  datetime(2017-01-05), 8,
  datetime(2017-01-05T13:40), 13,
  datetime(2017-01-06), 4,
  datetime(2017-01-07), 3,
  datetime(2017-01-08), 8,
  datetime(2017-01-08T21:00), 8,
  datetime(2017-01-09), 2,
  datetime(2017-01-09T12:00), 11,
  datetime(2017-01-10T05:00), 5,
];
let interval = 1d;
let stime = datetime(2017-01-01);
let etime = datetime(2017-01-10);
data
| limit 0
| make-series kind=nonempty avg(metric) default=1.0 on timestamp from stime to etime step interval 
```

|avg_metric|タイムスタンプ|
|---|---|
|[<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0<br>]|[<br>  "2017-01-01T00:00:00.0000000Z",<br>  "2017-01-02T00:00:00.0000000Z",<br>  "2017-01-03T00:00:00.0000000Z",<br>  "2017-01-04T00:00:00.0000000Z",<br>  "2017-01-05T00:00:00.0000000Z",<br>  "2017-01-06T00:00:00.0000000Z",<br>  "2017-01-07T00:00:00.0000000Z",<br>  "2017-01-08T00:00:00.0000000Z",<br>  "2017-01-09T00:00:00.0000000Z"<br>]|
