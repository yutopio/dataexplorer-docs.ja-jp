---
title: series 演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのシリーズ作成演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 6ed841a6f47eb9a0a1e73182a3b9acd1c0209bd9
ms.sourcegitcommit: 313a91d2a34383b5a6e39add6c8b7fabb4f8d39a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90680764"
---
# <a name="make-series-operator"></a>make-series 演算子

指定した軸に沿って、指定した集計値の系列を作成します。

```kusto
T | make-series sum(amount) default=0, avg(price) default=0 on timestamp from datetime(2016-01-01) to datetime(2016-01-10) step 1d by fruit, supplier
```

## <a name="syntax"></a>構文

*T* `| make-series` [*MakeSeriesParamters*] [*列* `=` ] *集計* [ `default` `=` *DefaultValue*] [ `,` ...] `on` *軸列* [ `from` *開始*] [ `to` *終了*] `step` *ステップ* [ `by` [*列* `=` ] *groupexpression* [ `,` ...]]

## <a name="arguments"></a>引数

* *Column:* 結果列の省略可能な名前。 既定値は式から派生した名前です。
* *DefaultValue:* 値が存在しない場合に使用される既定値。 *列*に特定の値が含まれていない場合*は、結果では、配列*の対応する要素に*DefaultValue*が割り当てられます。 *DefaultValue*を省略した場合、0が想定されます。 
* *集計:* 列名を引数として持つ、やなどの [集計関数](make-seriesoperator.md#list-of-aggregation-functions) の呼び出し `count()` `avg()` 。 [集計関数のリスト](make-seriesoperator.md#list-of-aggregation-functions)を参照してください。 演算子と共に使用できるのは、数値の結果を返す集計関数だけ `make-series` です。
* *軸列:* 系列が並べ替えられる列。 これはタイムラインと見なすことができ `datetime` ますが、すべての数値型にも適用されます。
* *start*: (省略可能) 構築する各系列の *軸列* の下限値。 *start*、 *end*、および *step* を使用して、指定された範囲内の *軸の列* の値の配列を構築し、指定した *手順*を使用します。 すべての *集計* 値は、この配列に対して順番に並べ替えられます。 この *軸列* 配列は、出力の最後の出力列でもあり、 *軸列*と同じ名前が付けられています。 *開始*値が指定されていない場合、開始は、各系列にデータを持つ最初の bin (ステップ) です。
* *end*: (省略可能) *軸列*の上限 (非包含) 値。 時系列の最後のインデックスは、この値よりも小さくなります (*先頭*には、*終了*よりも小さい*ステップ*の倍数の倍数が加算されます)。 *終了*値が指定されていない場合は、各系列にデータが含まれている最後のビン (ステップ) の上限になります。
* *ステップ*: *列* 配列の2つの連続する要素 (bin サイズ) の差。
* *Groupexpression:* 個別の値のセットを提供する列に対する式。 通常は、限られた値のセットが既に指定されている列名です。 
* *MakeSeriesParameters*: 0 個以上 (スペースで区切られた) パラメーターを*Name* `=` 、動作を制御する名前*値*の形式で指定します。 サポートされているパラメーターは次のとおりです。 
  
  |名前           |値                                        |[説明]                                                                                        |
  |---------------|-------------------------------------|------------------------------------------------------------------------------|
  |`kind`          |`nonempty`                               |系列演算子の入力が空の場合に、既定の結果を生成します|                                

## <a name="returns"></a>戻り値

入力行は、式と同じ値を持つグループに配置され、 `by` `bin_at(` *列*の `, ` *ステップ* `, ` *開始* `)` 式になります。 次に、指定された集計関数によってグループごとに計算が行われ、各グループに対応する行が生成されます。 結果には、列、列の列、 `by` および計算される集計ごとに少なくとも1つの列が含まれます。 *AxisColumn* (複数の列または数値以外の結果はサポートされていません)。

この中間結果には `by` 、と `bin_at(` *列*の `, ` *ステップ* `, ` *開始*値の個別の組み合わせが存在するのと同じ数の行が含ま `)` れます。

最後に、中間結果の行が、式の同じ値を持つグループに配置され、 `by` すべての集計値が配列 (型の値) に配置され `dynamic` ます。 集計ごとに、同じ名前の配列を含む列が1つ存在します。 すべての *軸の列* の値を持つ range 関数の出力の最後の列。 この値はすべての行に対して繰り返されます。 

既定値による欠損ビンの塗りつぶしにより、結果として得られるピボットテーブルのビン数 (つまり、集計値) はすべての系列に対して同じになります。  

**注:**

集計式とグループ化式の両方に任意の式を指定できますが、単純な列名を使用する方が効率的です。

**代替構文**

*T* `| make-series` [*column* `=` ]*集計*[ `default` `=` *DefaultValue*] [ `,` ...] `on` *軸列*の `in` `range(` *開始* `,` *停止* `,` *ステップ* `)` [ `by` [*列* `=` ] *groupexpression* [ `,` ...]]

別の構文から生成された系列は、次の2つの点で主な構文と異なります。
* *停止*値は包括的です。
* インデックス軸のビン分割は bin () で生成され、bin_at () ではありません。これは、生成された系列に *start* が含まれていないことを意味します。

別の構文ではなく、make シリーズの主要な構文を使用することをお勧めします。

**配布とシャッフル**

`make-series``summarize`構文 shufflekey を使用した[shufflekey ヒント](shufflequery.md)をサポートします。

## <a name="list-of-aggregation-functions"></a>集計関数の一覧

|機能|[説明]|
|--------|-----------|
|[any ()](any-aggfunction.md)|グループの空でないランダムな値を返します|
|[avg()](avg-aggfunction.md)|グループ全体の平均値を返します|
|[avgif()](avgif-aggfunction.md)|グループの述語を使用して平均を返します|
|[count()](count-aggfunction.md)|グループの数を返します|
|[countif()](countif-aggfunction.md)|グループの述語を使用してカウントを返します。|
|[dcount()](dcount-aggfunction.md)|グループ要素の概数を返します。|
|[dcountif()](dcountif-aggfunction.md)|グループの述語を使用して、おおよその個別のカウントを返します。|
|[max()](max-aggfunction.md)|グループ全体の最大値を返します|
|[maxif()](maxif-aggfunction.md)|グループの述語を使用して最大値を返します。|
|[min()](min-aggfunction.md)|グループ全体の最小値を返します|
|[minif()](minif-aggfunction.md)|グループの述語を使用して最小値を返します。|
|[stdev()](stdev-aggfunction.md)|グループ全体の標準偏差を返します|
|[sum()](sum-aggfunction.md)|グループ内の要素の合計を返します。|
|[sumif()](sumif-aggfunction.md)|グループの述語を使用して、要素の合計を返します。|
|[variance()](variance-aggfunction.md)|グループ間の分散を返します。|

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

|機能|[説明]|
|--------|-----------|
|[series_fill_backward()](series-fill-backwardfunction.md)|系列内の欠損値の後方塗りつぶし補間を実行します|
|[series_fill_const()](series-fill-constfunction.md)|系列の欠損値を指定された定数値に置き換えます|
|[series_fill_forward()](series-fill-forwardfunction.md)|系列内の欠損値の前方フィル補間を実行します|
|[series_fill_linear()](series-fill-linearfunction.md)|系列内の欠損値の線形補間を実行します|

* 注: 補間関数 `null` は、既定では欠損値として想定されます。 したがって `default=` *double* `null` `make-series` 、系列に補間関数を使用する場合は、に double () を指定します。 

## <a name="example"></a>例
  
 指定された範囲のタイムスタンプによって並べ替えられた各サプライヤーからの各果物の数と平均価格の配列を示すテーブル。 果物と supplier の個別の組み合わせごとに、出力に行があります。 出力列には、カウント、平均、およびタイムライン全体 (2016-01-01 から2016-01-10 まで) の果物、仕入先、および配列が表示されます。 すべての配列はそれぞれのタイムスタンプによって並べ替えられ、すべてのギャップに既定値が設定されます (この例では 0)。 他のすべての入力列は無視されます。
  
```kusto
T | make-series PriceAvg=avg(Price) default=0
on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

:::image type="content" source="images/make-seriesoperator/makeseries.png" alt-text="Makeseries":::  

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
  
|avg_metric|timestamp|
|---|---|
|[4.0、3.0、5.0、0.0、10.5、4.0、3.0、8.0、6.5]|[2017-01-01T00:00: 00.0000000 Z]、"2017-01-02T00:00: 00.0000000 Z"、"2017-01-03T00:00: 00.0000000 Z"、"2017-01-04T00:00: 00.0000000 Z"、"2017-01-05T00:00: 00.0000000 Z"、"2017-01-06T00:00: 00.0000000 Z"、"2017-01-07T00:00: 00.0000000 Z"、"2017-01-08T00:00: 00.0000000 Z"、"2017-01-09T00:00: 00.0000000 Z"]|  


へ `make-series` の入力が空の場合、の既定の動作で `make-series` も空の結果が生成されます。

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


でを使用する `kind=nonempty` `make-series` と、既定値の空ではない結果が生成されます。

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

|avg_metric|timestamp|
|---|---|
|[<br>  1.0、<br>  1.0、<br>  1.0、<br>  1.0、<br>  1.0、<br>  1.0、<br>  1.0、<br>  1.0、<br>  1.0<br>]|[<br>  "2017-01-01T00:00: 00.0000000 Z",<br>  "2017-01-02T00:00: 00.0000000 Z",<br>  "2017-01-03T00:00: 00.0000000 Z",<br>  "2017-01-04T00:00: 00.0000000 Z",<br>  "2017-01-05T00:00: 00.0000000 Z",<br>  "2017-01-06T00:00: 00.0000000 Z",<br>  "2017-01-07T00:00: 00.0000000 Z",<br>  "2017-01-08T00:00: 00.0000000 Z",<br>  "2017-01-09T00:00: 00.0000000 Z"<br>]|
