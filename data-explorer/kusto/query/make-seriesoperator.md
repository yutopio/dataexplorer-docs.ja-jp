---
title: メイクシリーズオペレーター - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで系列を作成する演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 3fa8f1693b56fc0820b9e0ba6b5f03a9363c9b98
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663720"
---
# <a name="make-series-operator"></a>make-series 演算子

指定した軸に沿って、指定された集計値の系列を作成します。 

```kusto
T | make-series sum(amount) default=0, avg(price) default=0 on timestamp from datetime(2016-01-01) to datetime(2016-01-10) step 1d by fruit, supplier
```

**構文**

*T* `| make-series` [*MakeSeriesParamters*] [`default` `=` *列*`=`]`,` *集計*[*デフォルト値*] [ ..]`on`*軸列*`from` *start*[ 開始`to`] `step` [`by` *終了*]*ステップ*[*[ 列*`=`]*グループ式*[`,` ..]]

**引数**

* *Column:* 結果列の省略可能な名前。 既定値は式から派生した名前です。
* *既定値:* 欠けている値の代わりに使用されるデフォルト値。 *AxisColumn*および*GroupExpression*の特定の値を持つ行がない場合、結果では、配列の対応する要素が*DefaultValue*で割り当てられます。 `default =`*既定値*を省略すると 0 が想定されます。 
* *集約:* 列名を引数として使用する、 `count()` `avg()`または などの[集計関数](make-seriesoperator.md#list-of-aggregation-functions)の呼び出し。 [集計関数のリスト](make-seriesoperator.md#list-of-aggregation-functions)を参照してください。 演算子と共`make-series`に使用できるのは、数値の結果を返す集計関数だけです。
* *軸列:* 系列の順序付け先となる列。 これはタイムラインと見なされますが、数値型`datetime`以外にも受け入れられます。
* *start*: (オプション) 各系列の*AxisColumn*の低境界値が構築されます。 *start* *、end、* および*step*は、指定された範囲内で指定された*ステップ*を使用して *、AxisColumn*値の配列を作成するために使用されます。 すべての*集計*値は、この配列にそれぞれ順序付けられます。 この*AxisColumn*配列は、出力内の最後の出力列で *、AxisColumn*と同じ名前を持つ列でもあります。 *開始*値が指定されていない場合、開始は、各系列にデータを持つ最初のビン (ステップ) です。
* *end*: (オプション) *AxisColumn*の高いバインド (非包括) 値の場合、時系列の最後のインデックスはこの値より小*start*さくなります (*および開始と終了*より小さい*ステップ*の整数倍)。 *終了*値を指定しない場合、各系列ごとにデータを持つ最後のビン (ステップ) の上限になります。
* *ステップ*: *AxisColumn*配列の連続する 2 つの要素の違い (ビンサイズ)。
* *GroupExpression:* 列に対する式です。個別の値のセットを示します。 通常は、限られた値のセットが既に指定されている列名です。 
* *MakeSeriesParameters*: 動作を制御する*名前*`=`*の値*の形式で 0 個以上 (スペースで区切られた) パラメーター。 サポートされているパラメーターは次のとおりです。 
  
  |名前           |値                                        |説明                                                                                        |
  |---------------|-------------------------------------|------------------------------------------------------------------------------|
  |`kind`          |`nonempty`                               |系列の入力が空の場合にデフォルトの結果を生成します。|                                

**戻り値**

入力行は、式と`by``bin_at(` *AxisColumn*`, `*ステップ*`, `*開始*`)`式と同じ値を持つグループに配置されます。 次に、指定された集計関数によってグループごとに計算が行われ、各グループに対応する行が生成されます。 結果には、`by`列 *、AxisColumn 列*、および計算された集計ごとに少なくとも 1 つの列が含まれます。 (複数の列または数値以外の結果がサポートされていない集計。

この中間結果には *、AxisColumn*`, `*ステップ*`, `*開始*`)`値との異なる`bin_at(`組み合わせがある行数と同じ数の`by`行があります。

最後に、中間結果の行が、式とすべての集計値の同`by`じ値を持つグループに配置され、配列(型の`dynamic`値)に配置されます。 集計ごとに、同じ名前の配列を含む 1 つの列があります。 すべての*AxisColumn*値を持つ範囲関数の出力の最後の列。 すべての行に対して値が繰り返されます。 

デフォルト値によってビンが欠落しているため、結果のピボット テーブルはすべての系列に同じ数のビン (つまり、集計値) を持ちます。  

**注**

集計式とグループ化式の両方に任意の式を指定できますが、単純な列名を使用する方が効率的です。

**代替構文**

*T* `| make-series` [*列*`=``default` `=` ]*集計*`,` [*デフォルト値*] [ ..]`on`*軸列*`in``,`*stop*`by``,`*start*`)``=`*GroupExpression**Column**step*開始停止`,`ステップ [ [ 列 ] グループ式 [ .]] `range(`

代替構文から生成される系列は、2 つの側面での主な構文とは異なります。
* *ストップ*値は包含です。
* インデックス軸をビニングすると bin() ではなく、bin_at() で生成されるため、生成された系列に*開始*が含まれる保証はありません。

代替構文ではなく、make-series の主な構文を使用することをお勧めします。

**分布とシャッフル**

`make-series`は`summarize`、シンタックスヒントを使用して[シャッフルキーヒント](shufflequery.md)をサポートしています。

## <a name="list-of-aggregation-functions"></a>集計関数の一覧

|機能|説明|
|--------|-----------|
|[any()](any-aggfunction.md)|グループのランダムな非空値を返します。|
|[avg()](avg-aggfunction.md)|グループ全体の平均値を返す|
|[count()](count-aggfunction.md)|グループのカウントを返します。|
|[countif()](countif-aggfunction.md)|グループの述語でカウントを返します。|
|[dcount()](dcount-aggfunction.md)|グループ要素のおおよその個別のカウントを返します。|
|[max()](max-aggfunction.md)|グループ全体の最大値を返します。|
|[min()](min-aggfunction.md)|グループ全体の最小値を返します。|
|[stdev()](stdev-aggfunction.md)|グループ全体の標準偏差を返します。|
|[sum()](sum-aggfunction.md)|グループを使用する要素の合計を返します。|
|[variance()](variance-aggfunction.md)|グループ全体の分散を返します。|

## <a name="list-of-series-analysis-functions"></a>シリーズ解析関数一覧

|機能|説明|
|--------|-----------|
|[series_fir()](series-firfunction.md)|[有限インパルス応答フィルタを](https://en.wikipedia.org/wiki/Finite_impulse_response)適用|
|[series_iir()](series-iirfunction.md)|[無限インパルス応答フィルタを](https://en.wikipedia.org/wiki/Infinite_impulse_response)適用|
|[series_fit_line()](series-fit-linefunction.md)|入力の最も近似値である直線を検索します。|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|動的オブジェクトを返す入力の最適な近似値である行を検索します。|
|[series_fit_2lines()](series-fit-2linesfunction.md)|入力の最も近似値である 2 つの行を検索します。|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|入力の最も近似値である 2 つの行を検索し、動的オブジェクトを返します。|
|[series_outliers()](series-outliersfunction.md)|シリーズの異常ポイントをスコア|
|[series_periods_detect()](series-periods-detectfunction.md)|時系列に存在する最も重要な期間を検索する|
|[series_periods_validate()](series-periods-validatefunction.md)|時系列に指定された長さの周期パターンが含まれているかどうかをチェックします。|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|共通の統計値を持つ複数の列を返す (最小/最大/分散/stdev/平均)|
|[series_stats()](series-statsfunction.md)|共通の統計量 (最小/最大/分散/stdev/平均) を使用して動的な値を生成します。|
  
## <a name="list-of-series-interpolation-functions"></a>系列補間関数の一覧
|機能|説明|
|--------|-----------|
|[series_fill_backward()](series-fill-backwardfunction.md)|系列内の欠損値の逆方向の埋め込み補間を実行します。|
|[series_fill_const()](series-fill-constfunction.md)|系列内の欠損値を指定した定数値で置き換えます。|
|[series_fill_forward()](series-fill-forwardfunction.md)|系列内の欠損値の順方向フィル補間を実行します。|
|[series_fill_linear()](series-fill-linearfunction.md)|系列内の欠損値の線形補間を実行します。|

* 注: 補間関数は、デフォルト`null`では欠損値と見なされます。 したがって、系列の補間`default=`関数を`null`使用する`make-series`場合は *、で double*( ) を指定することをお勧めします。 

## <a name="example"></a>例
  
 指定された範囲のタイムスタンプで並べ替えられた各サプライヤーの各果物の数と平均価格の配列を示すテーブル。 果物とサプライヤーのそれぞれの異なる組み合わせの出力に行があります。 出力列には、果物、サプライヤー、アレイが表示されます: カウント、平均、および全体のタイムライン (2016-01-01 から 2016-01-10 まで)。 すべての配列はそれぞれのタイムスタンプでソートされ、すべてのギャップはデフォルト値(この例では0)で埋められます。 他のすべての入力列は無視されます。
  
```kusto
T | make-series PriceAvg=avg(Price) default=0
on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

:::image type="content" source="images/aggregations/makeseries.png" alt-text="メイクシリーズ":::  
  
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
  
|avg_metric|timestamp|
|---|---|
|[ 4.0, 3.0, 5.0, 0.0, 10.5, 4.0, 3.0, 8.0, 6.5 ]|【 "2017-01-01T00:00:00:00.000000Z" ", "2017-01-02T00:00:00.000000Z" "2017-01-03T000:00:00:00.000000Z", "2017-01-04T00:00:00.000000Z", "2017-01-05T000:00:00:000000Z", "2017-01-06T00:00:00:00.000000Z", "2017-01-07T00:00:00.000000Z", "" 2017-01-08T00:00:00:00.000000Z,","2017-01-09T00:00:00.000000Z"|  


入力が空`make-series`の場合、デフォルトの振る舞`make-series`いは空の結果も生成します。

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


を`kind=nonempty``make-series`使用すると、デフォルト値の空でない結果が生成されます。

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

|avg_metric|timestamp|
|---|---|
|[<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0<br>]|[<br>  "2017-01-01T00:00:00.0000000Z",<br>  "2017-01-02T00:00:00.0000000Z",<br>  "2017-01-03T00:00:00.0000000Z",<br>  "2017-01-04T00:00:00.0000000Z",<br>  "2017-01-05T00:00:00.0000000Z",<br>  "2017-01-06T00:00:00.0000000Z",<br>  "2017-01-07T00:00:00.0000000Z",<br>  "2017-01-08T00:00:00.0000000Z",<br>  "2017-01-09T00:00:00.0000000Z"<br>]|