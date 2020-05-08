---
title: series 演算子-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのシリーズ作成演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 66211cfcb33f97ba5f58d82e7ee4a8a8c7631e96
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618439"
---
# <a name="make-series-operator"></a>make-series 演算子

指定した軸に沿って、指定した集計値の系列を作成します。 

```kusto
T | make-series sum(amount) default=0, avg(price) default=0 on timestamp from datetime(2016-01-01) to datetime(2016-01-10) step 1d by fruit, supplier
```

**構文**

*T* `| make-series` [*MakeSeriesParamters*] [*列* `=`]*集計*[`default` `=` *DefaultValue*] [`,` ...]`on` *軸列*[`from` *開始*] [`to` *終了*] `step` *ステップ*[`by` [*列* `=`] *groupexpression* [`,` ...]]

**引数**

* *Column:* 結果列の省略可能な名前。 既定値は式から派生した名前です。
* *DefaultValue:* 既定値は、値が存在しない場合に使用されます。 値が指定さ*れている*行がない場合は *、結果*内で、配列の対応する要素が*DefaultValue*で割り当てられます。 `default =` *DefaultValue*を省略すると、0が想定されます。 
* *集計:* 列名を引数として持つ`count()` 、 `avg()`やなどの[集計関数](make-seriesoperator.md#list-of-aggregation-functions)の呼び出し。 [集計関数のリスト](make-seriesoperator.md#list-of-aggregation-functions)を参照してください。 演算子と共`make-series`に使用できるのは、数値の結果を返す集計関数だけであることに注意してください。
* *軸列:* 系列が並べ替えられる列。 これはタイムラインと見なすことができ`datetime`ますが、すべての数値型にも適用されます。
* *start*: (省略可能) 各系列の*列*の下限値が作成されます。 *start*、 *end* 、 *step*を使用して、指定された範囲内の*軸列*の値の配列を構築し、指定した*手順*を使用します。 すべての*集計*値は、この配列に対して順番に並べ替えられます。 この*列*配列は、出力の最後の出力列でも、*軸列*と同じ名前になっています。 *開始*値が指定されていない場合、開始は、各系列にデータを持つ最初の bin (ステップ) です。
* *end*: (省略可能)*軸列*の下限 (非包含) 値。時系列の最後のインデックスは、この値よりも小さくなります (*開始*されるのは、*終了*よりも小さい*ステップ*の倍数になります)。 *終了*値が指定されていない場合は、各系列にデータを含む最後のビン (ステップ) の上限になります。
* *ステップ*:*列*配列の2つの連続する要素 (bin サイズ) の差。
* *GroupExpression:* 列に対する式です。個別の値のセットを示します。 通常は、限られた値のセットが既に指定されている列名です。 
* *MakeSeriesParameters*: 0 個以上 (スペースで区切られた) パラメーターを、動作を制御する*名前* `=` *値*の形式で指定します。 サポートされているパラメーターは次のとおりです。 
  
  |名前           |値                                        |説明                                                                                        |
  |---------------|-------------------------------------|------------------------------------------------------------------------------|
  |`kind`          |`nonempty`                               |系列演算子の入力が空の場合に、既定の結果を生成します|                                

**戻り値**

入力行は、 `by`式および`bin_at(`*軸列*`, `*ステップ*`, `の*開始*`)`式の値が同じグループに配置されます。 次に、指定された集計関数によってグループごとに計算が行われ、各グループに対応する行が生成されます。 結果には、 `by` *列、列*の列、および計算される集計ごとに少なくとも1つの列が含まれます。 (複数の列または数値以外の結果はサポートされていません)。

`by`この中間結果には、と`bin_at(`*列*`, `*ステップ*`, `*開始*`)`値の個別の組み合わせが存在するのと同じ数の行が含まれます。

最後に、中間結果の行が、 `by`式の同じ値を持つグループに配置され、すべての集計値が配列 ( `dynamic`型の値) に配置されます。 各集計に対して、同じ名前の配列を含む列が1つ存在します。 すべての*軸の列*の値を持つ range 関数の出力の最後の列。 この値はすべての行に対して繰り返されます。 

既定値によって欠損ビンがいっぱいになるため、結果として得られるピボットテーブルのビン数 (つまり集計値) はすべての系列で同じであることに注意してください。  

**注**

集計式とグループ化式の両方に任意の式を指定できますが、単純な列名を使用する方が効率的です。

**代替構文**

*T* `| make-series` [*列* `=`]*集計*[`default` `=` *DefaultValue*] [`,` ...]`on` *列*`,` *step* `,` *stop* `=` *start* `)` *GroupExpression* *Column*の開始`,`停止ステップ [`by` [列] groupexpression [...]] `in` `range(`

別の構文から生成された系列は、次の2つの側面の主な構文と異なります。
* *停止*値は包括的です。
* インデックス軸のビン分割は bin () で生成され、bin_at () ではありません。つまり、*開始*は、生成された系列に含まれるとは限りません。

別の構文ではなく、make シリーズの主要な構文を使用することをお勧めします。

**配布とシャッフル**

`make-series`構文`summarize` shufflekey を使用した[shufflekey ヒント](shufflequery.md)をサポートします。

## <a name="list-of-aggregation-functions"></a>集計関数の一覧

|機能|[説明]|
|--------|-----------|
|[any ()](any-aggfunction.md)|グループに対して空でないランダムな値を返します|
|[avg ()](avg-aggfunction.md)|Retuns グループ全体の平均値|
|[count ()](count-aggfunction.md)|グループの数を返します|
|[countif()](countif-aggfunction.md)|グループの述語を使用して count を返します|
|[dcount()](dcount-aggfunction.md)|グループ要素の概数の個別カウントを返します。|
|[max ()](max-aggfunction.md)|グループ全体の最大値を返します|
|[min ()](min-aggfunction.md)|グループ全体の最小値を返します|
|[stdev ()](stdev-aggfunction.md)|グループ全体の標準偏差を返します|
|[sum ()](sum-aggfunction.md)|グループので要素の合計を返します。|
|[分散 ()](variance-aggfunction.md)|グループ間の分散を返します。|

## <a name="list-of-series-analysis-functions"></a>系列分析関数の一覧

|機能|[説明]|
|--------|-----------|
|[series_fir()](series-firfunction.md)|[有限インパルス応答](https://en.wikipedia.org/wiki/Finite_impulse_response)フィルターを適用します|
|[series_iir()](series-iirfunction.md)|[無限インパルス応答](https://en.wikipedia.org/wiki/Infinite_impulse_response)フィルターを適用します|
|[series_fit_line()](series-fit-linefunction.md)|入力に最も近い近似線を検索します。|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|入力に最も近い行を検索し、動的オブジェクトを返します。|
|[series_fit_2lines()](series-fit-2linesfunction.md)|入力の中で最も近似的な2行を検索します。|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|入力の近似値である2つの行を検索し、動的なオブジェクトを返します。|
|[series_outliers()](series-outliersfunction.md)|シリーズ内の異常な点にスコアを付けます|
|[series_periods_detect()](series-periods-detectfunction.md)|タイムシリーズに存在する最も重要な期間を検索します。|
|[series_periods_validate()](series-periods-validatefunction.md)|時系列に特定の長さの定期的なパターンが含まれているかどうかを確認します。|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|共通の統計情報を含む複数の列を返す (最小/最大/分散/stdev/平均)|
|[series_stats()](series-statsfunction.md)|共通の統計情報 (最小/最大/分散/stdev/平均) を使用して動的な値を生成します。|
  
## <a name="list-of-series-interpolation-functions"></a>系列補間関数の一覧
|機能|説明|
|--------|-----------|
|[series_fill_backward()](series-fill-backwardfunction.md)|系列内の欠損値の後方塗りつぶし補間を実行します|
|[series_fill_const()](series-fill-constfunction.md)|系列の欠損値を指定された定数値に置き換えます|
|[series_fill_forward()](series-fill-forwardfunction.md)|系列内の欠損値の前方フィル補間を実行します|
|[series_fill_linear()](series-fill-linearfunction.md)|系列内の欠損値の線形補間を実行します|

* 注: 補間関数は、 `null`既定では欠損値として想定されます。 そのため、系列に補`default=`間関数`null`を使用`make-series`する場合は、に*double*() を指定することをお勧めします。 

## <a name="example"></a>例
  
 指定された範囲のタイムスタンプによって並べ替えられた各サプライヤーからの各果物の数と平均価格の配列を示すテーブル。 果物と supplier の個別の組み合わせごとに、出力に行があります。 出力列には、[カウント]、[平均]、および [タイムライン全体] (2016-01-01 から2016-01-10 まで) のフルーツ、仕入先、および配列が表示されます。 すべての配列はそれぞれのタイムスタンプによって並べ替えられ、すべてのギャップに既定値が設定されます (この例では 0)。 他のすべての入力列は無視されます。
  
```kusto
T | make-series PriceAvg=avg(Price) default=0
on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

:::image type="content" source="images/make-seriesoperator/makeseries.png" alt-text="Makeseries":::  
  
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
|[4.0、3.0、5.0、0.0、10.5、4.0、3.0、8.0、6.5]|[2017-01-01T00:00: 00.0000000 Z]、"2017-01-02T00:00: 00.0000000 Z"、"2017-01-03T00:00: 00.0000000 Z"、"2017-01-04T00:00: 00.0000000 Z"、"2017-01-05T00:00: 00.0000000 Z"、"2017-01-06T00:00: 00.0000000 Z"、"2017-01-07T00:00: 00.0000000 Z"、"2017-01-08T00:00: 00.0000000 Z"、"2017-01-09T00:00: 00.0000000 Z"]|  


へ`make-series`の入力が空の場合、の既定の`make-series`動作でも空の結果が生成されます。

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


で`kind=nonempty` `make-series`を使用すると、既定値の空ではない結果が生成されます。

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
|[<br>  1.0、<br>  1.0、<br>  1.0、<br>  1.0、<br>  1.0、<br>  1.0、<br>  1.0、<br>  1.0、<br>  1.0<br>]|[<br>  "2017-01-01T00:00: 00.0000000 Z",<br>  "2017-01-02T00:00: 00.0000000 Z",<br>  "2017-01-03T00:00: 00.0000000 Z",<br>  "2017-01-04T00:00: 00.0000000 Z",<br>  "2017-01-05T00:00: 00.0000000 Z",<br>  "2017-01-06T00:00: 00.0000000 Z",<br>  "2017-01-07T00:00: 00.0000000 Z",<br>  "2017-01-08T00:00: 00.0000000 Z",<br>  "2017-01-09T00:00: 00.0000000 Z"<br>]|