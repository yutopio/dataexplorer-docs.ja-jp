---
title: 集計の中間結果のパーティション分割と作成 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで集計の中間結果をパーティション分割し、作成する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c906e764330efedac051587201b28e5d0fc1ef07
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766139"
---
# <a name="partitioning-and-composing-intermediate-results-of-aggregations"></a>集計の中間結果のパーティション分割と作成

過去 7 日間の個別ユーザー数を毎日計算するとします。 これを行う 1 つの方法は`summarize dcount(user)`、過去 7 日間にフィルター処理されたスパンで 1 日 1 回実行する方法です。 計算を実行するたびに、前の計算と 6 日間の重複があるため、これは非効率的です。 別のオプションは、日ごとに集計を計算し、効率的な方法でこれらの集計を結合することです。 このオプションでは、最後の 6 つの結果を "記憶" する必要がありますが、はるかに効率的です。

このようなクエリをパーティション分割することは、count() や sum() などの単純な集計では簡単です。 ただし、dcount() やパーセンタイル() など、より複雑な集計に対して実行することもできます。 このトピックでは、Kusto がこのような計算をサポートする方法について説明します。

次の例は、hll/tdigest を使用する方法を示し、一部のシナリオでこれらを使用すると、他の方法よりもはるかにパフォーマンスが高い場合があることを示しています。

> [!NOTE]
> 場合によっては、`hll`または`tdigest`集計関数によって生成される動的オブジェクトが大きく、エンコーディング ポリシーの既定の MaxValueSize プロパティを超える場合があります。 その場合、オブジェクトは null として取り込まれます。
たとえば、関数の`hll`出力を精度レベル 4 で永続化する場合、hll オブジェクトのサイズは既定の MaxValueSize を超えています。

```kusto
range x from 1 to 1000000 step 1
| summarize hll(x,4)
| project sizeInMb = estimate_data_size(hll_x) / pow(1024,2)
```

|sizeInMb|
|---|
|1.0000524520874|

この種のポリシーを適用する前にテーブルにこれを取り込むには、null が取り込まれます。

```kusto
.set-or-append MyTable <| range x from 1 to 1000000 step 1
| summarize hll(x,4)
```

```kusto
MyTable
| project isempty(hll_x)
```

| Column1 |
|---------|
| 1       |

これを回避するには、次のように MaxValueSize`bigobject`を 2 MB に上書きする特殊なエンコード ポリシー の種類を使用します。

```kusto
.alter column MyTable.hll_x policy encoding type='bigobject'
```



したがって、上記の同じ表に値を取り込む:

```kusto
.set-or-append MyTable <| range x from 1 to 1000000 step 1
| summarize hll(x,4)
```
2 番目の値を正常に取り込みます。 

```kusto
MyTable
| project isempty(hll_x)
```

|Column1|
|---|
|1|
|0|


**例**

各時間に表示されるページに対する hll 値を持つテーブル PageViewsHllTDigest があるとします。 目的は、これらの値を取得することですが、ビン分割`12h`して にして、タイムスタンプによって hll_merge() 集計関数を使用して hll 値をマージします`12h`。 次に、関数を呼び出dcount_hll最後の dcount 値を取得します。

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 12h)
| project Timestamp , dcount_hll(merged_hll)
```

|Timestamp|dcount_hll_merged_hll|
|---|---|
|2016-05-01 12:00:00.0000000|20056275|
|2016-05-02 00:00:00.0000000|38797623|
|2016-05-02 12:00:00.0000000|39316056|
|2016-05-03 00:00:00.0000000|13685621|

または、ビン分割されたタイムスタンプの`1d`場合も:

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 1d)
| project Timestamp , dcount_hll(merged_hll)
```

|Timestamp|dcount_hll_merged_hll|
|---|---|
|2016-05-01 00:00:00.0000000|20056275|
|2016-05-02 00:00:00.0000000|64135183|
|2016-05-03 00:00:00.0000000|13685621|

 同じことが、毎時間のBytesDeliveredを表す消化器の値に対して行われる可能性があります。

```kusto
PageViewsHllTDigest
| summarize merged_tdigests = merge_tdigests(tdigestBytesDel) by bin(Timestamp, 12h)
| project Timestamp , percentile_tdigest(merged_tdigests, 95, typeof(long))
```

|Timestamp|percentile_tdigest_merged_tdigests|
|---|---|
|2016-05-01 12:00:00.0000000|170200|
|2016-05-02 00:00:00.0000000|152975|
|2016-05-02 12:00:00.0000000|181315|
|2016-05-03 00:00:00.0000000|146817|
 
**例**

Kusto の制限に達すると、データセットに対して定期的にクエリを実行する必要がありますが、これらの大きなデータセットを計算[`percentile()`](percentiles-aggfunction.md)または[`dcount()`](dcount-aggfunction.md)上の通常のクエリを実行する必要があるデータセットが大きすぎると、制限に達します。

::: zone pivot="azuredataexplorer"

この問題を解決するために、新しく追加されたデータは、必要な操作がdcountの場合、または[`hll()`](hll-aggfunction.md)[`tdigest()`](tdigest-aggfunction.md)、必要な操作が百分位数である場合、または[`set/append`](../management/data-ingestion/index.md)[`update policy`](../management/updatepolicy.md)を使用して、一時テーブルに hll または tdigest 値として追加される可能性があります。

::: zone-end

::: zone pivot="azuremonitor"

この問題を解決するために、新しく追加されたデータは、必要な操作がdcountのときにhll[`hll()`](hll-aggfunction.md)または消化器値として一時テーブルに追加される可能性があり、この場合、dcount の中間結果は別のデータセットに保存され、ターゲットの大きなデータよりも小さいはずです。

::: zone-end

これらの値の最終結果を取得する必要がある場合、クエリは hll/tdigest の合併を使用[`hll-merge()`](hll-merge-aggfunction.md)/[`tdigest_merge()`](tdigest-merge-aggfunction.md)する可能性があります: 、マージされた値[`percentile_tdigest()`](percentile-tdigestfunction.md) / [`dcount_hll()`](dcount-hllfunction.md)を取得した後、これらのマージされた値に対して呼び出され、dcount またはパーセンタイルの最終結果を取得できます。

テーブルが存在すると仮定すると、毎日データが取り込まれる PageViews で、日より後の 1 つの最小数あたりの表示ページ数を計算する日 ( 2016-05-01 18:00:00.000000) 。

次のクエリを実行します。

```kusto
PageViews   
| where Timestamp > datetime(2016-05-01 18:00:00.0000000)
| summarize percentile(BytesDelivered, 90), dcount(Page,2) by bin(Timestamp, 1d)
```

|Timestamp|percentile_BytesDelivered_90|dcount_Page|
|---|---|---|
|2016-05-01 00:00:00.0000000|83634|20056275|
|2016-05-02 00:00:00.0000000|82770|64135183|
|2016-05-03 00:00:00.0000000|72920|13685621|

このクエリは、このクエリを実行するたびにすべての値を集計します (たとえば、1 日に何回も実行する場合)。

hll 値と tdigest 値 (dcount とパーセンタイルの中間結果) を一時テーブル PageViewsHllTDigest に保存する場合、更新ポリシーまたは set/append コマンドを使用して、値をマージし、次のクエリを使用してdcount_hll/percentile_tdigestを使用します。

```kusto
PageViewsHllTDigest
| summarize  percentile_tdigest(merge_tdigests(tdigestBytesDel), 90), dcount_hll(hll_merge(hllPage)) by bin(Timestamp, 1d)
```

|Timestamp|percentile_tdigest_merge_tdigests_tdigestBytesDel|dcount_hll_hll_merge_hllPage|
|---|---|---|
|2016-05-01 00:00:00.0000000|84224|20056275|
|2016-05-02 00:00:00.0000000|83486|64135183|
|2016-05-03 00:00:00.0000000|72247|13685621|

これはよりパフォーマンスが高く、クエリはより小さいテーブル上で実行されます (この例では、最初のクエリは約 215M のレコードで実行され、2 番目のクエリは 32 レコードのみで実行されます)。

**例**

保持クエリ。
各ウィキペディアのページが閲覧された時刻 (サンプルサイズは 10M) を表し、各日付 1 の日付 2 で、日付 1 と日付 2 の両方でレビューされたページの割合を、日付 1 (日付 1 < 日付 2) に対して比較して見つけたいとします。
  
単純な方法では、結合演算子と集計演算子を使用します。

```kusto
// Get the total pages viewed each day
let totalPagesPerDay = PageViewsSample
| summarize by Page, Day = startofday(Timestamp)
| summarize count() by Day;
// Join the table to itself to get a grid where 
// each row shows foreach page1, in which two dates
// it was viewed.
// Then count the pages between each two dates to
// get how many pages were viewed between date1 and date2.
PageViewsSample
| summarize by Page, Day1 = startofday(Timestamp)
| join kind = inner
(
    PageViewsSample
    | summarize by Page, Day2 = startofday(Timestamp)
)
on Page
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

|Day1|Day2|パーセント|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|34.0645725975255|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.618368960101|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.6291376489636|

 
上記のクエリは約 18 秒かかりました。

と の関数を[`hll()`](hll-aggfunction.md)使用すると、同等のクエリは約 1.3 秒後に終了し、hll 関数は上記のクエリを約 14 倍に高速化することを示します。 [`dcount_hll()`](dcount-hllfunction.md) [`hll_merge()`](hll-merge-aggfunction.md)

```kusto
let Stats=PageViewsSample | summarize pagehll=hll(Page, 2) by day=startofday(Timestamp); // saving the hll values (intermediate results of the dcount values)
let day0=toscalar(Stats | summarize min(day)); // finding the min date over all dates.
let dayn=toscalar(Stats | summarize max(day)); // finding the max date over all dates.
let daycount=tolong((dayn-day0)/1d); // finding the range between max and min
Stats
| project idx=tolong((day-day0)/1d), day, pagehll
| mv-expand pidx=range(0, daycount) to typeof(long)
// Extend the column to get the dcount value from hll'ed values for each date (same as totalPagesPerDay from the above query)
| extend key1=iff(idx < pidx, idx, pidx), key2=iff(idx < pidx, pidx, idx), pages=dcount_hll(pagehll)
// For each two dates, merge the hll'ed values to get the total dcount over each two dates, 
// This helps to get the pages viewed in both date1 and date2 (see the description below about the intersection_size)
| summarize (day1, pages1)=arg_min(day, pages), (day2, pages2)=arg_max(day, pages), union_size=dcount_hll(hll_merge(pagehll)) by key1, key2
| where day2 > day1
// To get pages viewed in date1 and also date2, look at the merged dcount of date1 and date2, subtract it from pages of date1 + pages on date2.
| project pages1, day1,day2, intersection_size=(pages1 + pages2 - union_size)
| project day1, day2, Percentage = intersection_size*100.0 / pages1
```

|1日目|2日目|パーセント|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|33.2298494510578|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.9773830213667|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.5160020350006|

> [!NOTE] 
> hll 関数のエラーにより、クエリの結果が 100% 正確ではありません。(エラー[`dcount()`](dcount-aggfunction.md)の詳細についてはを参照してください)。
