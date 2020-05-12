---
title: 集計の中間結果のパーティション分割と作成-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの集計の中間結果のパーティション分割と作成について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8085f2347501c313c857a262bc9de6ec7280c90c
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224546"
---
# <a name="partitioning-and-composing-intermediate-results-of-aggregations"></a>集計の中間結果のパーティション分割と作成

過去7日間にわたる個別のユーザーの数を毎日計算する場合。 `summarize dcount(user)`過去7日間にフィルター処理されたスパンを使用して1日に1回実行できます。 このメソッドは、計算が実行されるたびに、前の計算と6日間の重複があるため、効率的ではありません。 また、1日の集計を計算し、これらの集計を結合することもできます。 この方法では、最後の6つの結果を "記憶する" 必要がありますが、はるかに効率的です。

説明に従ってクエリをパーティション分割すると、やなどの簡単な集計を簡単に行うことが `count()` `sum()` できます。 また、やなどの複雑な集計にも役立ち `dcount()` `percentiles()` ます。 このトピックでは、Kusto でこのような計算をサポートする方法について説明します。

次の例で `hll` / `tdigest` は、これらのコマンドを使用して、一部のシナリオで非常に高いパフォーマンスを実現する方法を示します。

> [!NOTE]
> 場合によっては、または集計関数によって生成される動的オブジェクト `hll` `tdigest` が大きく、エンコードポリシーの既定の MaxValueSize プロパティを超えることがあります。 その場合、オブジェクトは null として取り込まれたされます。
たとえば、精度レベル4の関数の出力を永続化する場合、 `hll` オブジェクトのサイズが `hll` 既定の MaxValueSize (1 mb) を超えています。

```kusto
range x from 1 to 1000000 step 1
| summarize hll(x,4)
| project sizeInMb = estimate_data_size(hll_x) / pow(1024,2)
```

|sizeInMb|
|---|
|1.0000524520874|

この種類のポリシーを適用する前にこの取り込みをテーブルに挿入すると、null が表示されます。

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

取り込み null を回避するには、特殊なエンコードポリシーの種類を使用し `bigobject` ます。これは、次の `MaxValueSize` ように 2 MB にオーバーライドされます。

```kusto
.alter column MyTable.hll_x policy encoding type='bigobject'
```

次のように、同じテーブルに値を取り込みします。

```kusto
.set-or-append MyTable <| range x from 1 to 1000000 step 1
| summarize hll(x,4)
```

2番目の値を正常に取り込みします。 

```kusto
MyTable
| project isempty(hll_x)
```

|Column1|
|---|
|1|
|0|


**例**

`PageViewsHllTDigest` `hll` 1 時間ごとに表示されるページの値を含むテーブルがあります。 これらの値ビンをにする必要が `12h` あります。 `hll`集計関数を使用して値をマージ `hll_merge()` し、タイムスタンプビンをに結合し `12h` ます。 関数を使用し `dcount_hll` て、最終的な値を返し `dcount` ます。

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 12h)
| project Timestamp , dcount_hll(merged_hll)
```

|Timestamp|`dcount_hll_merged_hll`|
|---|---|
|2016-05-01 12:00: 00.0000000|20056275|
|2016-05-02 00:00: 00.0000000|38797623|
|2016-05-02 12:00: 00.0000000|39316056|
|2016-05-03 00:00: 00.0000000|13685621|

次のようにタイムスタンプをビン分割するには `1d` :

```kusto
PageViewsHllTDigest
| summarize merged_hll = hll_merge(hllPage) by bin(Timestamp, 1d)
| project Timestamp , dcount_hll(merged_hll)
```

|Timestamp|`dcount_hll_merged_hll`|
|---|---|
|2016-05-01 00:00: 00.0000000|20056275|
|2016-05-02 00:00: 00.0000000|64135183|
|2016-05-03 00:00: 00.0000000|13685621|

の値に対して同じクエリを実行できます `tdigest` 。これは、 `BytesDelivered` 1 時間以内にを表します。

```kusto
PageViewsHllTDigest
| summarize merged_tdigests = merge_tdigests(tdigestBytesDel) by bin(Timestamp, 12h)
| project Timestamp , percentile_tdigest(merged_tdigests, 95, typeof(long))
```

|Timestamp|`percentile_tdigest_merged_tdigests`|
|---|---|
|2016-05-01 12:00: 00.0000000|170200|
|2016-05-02 00:00: 00.0000000|152975|
|2016-05-02 12:00: 00.0000000|181315|
|2016-05-03 00:00: 00.0000000|146817|
 
**例**

Kusto の制限に達しているデータセットは、データセットに対して定期的なクエリを実行する必要がありますが、通常のクエリを実行して大規模なデータセットを計算する必要があり [`percentile()`](percentiles-aggfunction.md) [`dcount()`](dcount-aggfunction.md) ます。

::: zone pivot="azuredataexplorer"

この問題を解決するには、新たに追加されたデータを `hll` 、 `tdigest` [`hll()`](hll-aggfunction.md) 必要な操作がである場合はを使用し、または `dcount` を使用して [`tdigest()`](tdigest-aggfunction.md) 必要な操作 [`set/append`](../management/data-ingestion/index.md) [`update policy`](../management/updatepolicy.md) である場合は、を使用して、またはの値として一時テーブルに追加する この場合、またはの中間結果 `dcount` は `tdigest` 別のデータセットに保存されます。これは、ターゲットの大きな値よりも小さくする必要があります。

::: zone-end

::: zone pivot="azuremonitor"

この問題を解決するには、新しく追加されたデータが、 `hll` `tdigest` [`hll()`](hll-aggfunction.md) 必要な操作がのときにを使用して、またはの値として一時テーブルに追加されることがあり `dcount` ます。 この場合、の中間結果 `dcount` は別のデータセットに保存されます。これは、ターゲットの大きな値よりも小さくする必要があります。

::: zone-end

これらの値の最終的な結果を取得する必要がある場合、クエリは hll/tdigest の合併を使用することがあります。 [`hll-merge()`](hll-merge-aggfunction.md) / [`tdigest_merge()`](tdigest-merge-aggfunction.md) その後、マージされた値を取得した後、 [`percentile_tdigest()`](percentile-tdigestfunction.md)  /  [`dcount_hll()`](dcount-hllfunction.md) これらのマージされた値に対してを呼び出して、またはパーセンタイルの最終的な結果を取得することができます `dcount` 。

データが毎日取り込まれたされるテーブル、PageViews があると仮定して、日付 = datetime (2016-05-01 18:00: 00.0000000) より後に1分間に表示される個別のページ数を計算します。

次のクエリを実行します。

```kusto
PageViews   
| where Timestamp > datetime(2016-05-01 18:00:00.0000000)
| summarize percentile(BytesDelivered, 90), dcount(Page,2) by bin(Timestamp, 1d)
```

|Timestamp|percentile_BytesDelivered_90|dcount_Page|
|---|---|---|
|2016-05-01 00:00: 00.0000000|83634|20056275|
|2016-05-02 00:00: 00.0000000|82770|64135183|
|2016-05-03 00:00: 00.0000000|72920|13685621|

このクエリでは、このクエリを実行するたびにすべての値が集計されます (たとえば、1日に何度も実行する場合)。

`hll`との `tdigest` 値 (つまり、とパーセンタイルの中間結果) を一時テーブルに保存した場合、 `dcount` `PageViewsHllTDigest` 更新ポリシーまたは set/append コマンドを使用すると、値のみをマージしてから、 `dcount_hll` / `percentile_tdigest` 次のクエリを使用してを使用できます。

```kusto
PageViewsHllTDigest
| summarize  percentile_tdigest(merge_tdigests(tdigestBytesDel), 90), dcount_hll(hll_merge(hllPage)) by bin(Timestamp, 1d)
```

|Timestamp|`percentile_tdigest_merge_tdigests_tdigestBytesDel`|`dcount_hll_hll_merge_hllPage`|
|---|---|---|
|2016-05-01 00:00: 00.0000000|84224|20056275|
|2016-05-02 00:00: 00.0000000|83486|64135183|
|2016-05-03 00:00: 00.0000000|72247|13685621|

これにより、パフォーマンスが向上し、クエリがより小さなテーブルで実行されます (この例では、最初のクエリは ~ 215M のレコードを実行し、2つ目は32レコードを超えてのみ実行されます)。

**例**

保持クエリ。
各 Wikipedia ページが表示された日時を集計するテーブルがあると仮定します (サンプルサイズは10万)。各 date1 を検索する場合は、date1 に表示されるページに対して、date1 と date2 の両方でレビューされたページの割合 (date1 < date2) を確認します。
  
単純な方法では、join 演算子と集計演算子が使用されます。

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

|Day1|Day2|割合|
|---|---|---|
|2016-05-01 00:00: 00.0000000|2016-05-02 00:00: 00.0000000|34.0645725975255|
|2016-05-01 00:00: 00.0000000|2016-05-03 00:00: 00.0000000|16.618368960101|
|2016-05-02 00:00: 00.0000000|2016-05-03 00:00: 00.0000000|14.6291376489636|

 
上記のクエリの所要時間は18秒でした。

、、およびの関数を使用する場合、 [`hll()`](hll-aggfunction.md) [`hll_merge()`](hll-merge-aggfunction.md) 同等のクエリは [`dcount_hll()`](dcount-hllfunction.md) ~ 1.3 秒後に終了し、 `hll` 関数によって上記のクエリの速度が約14倍になることが示されます。

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

|day1|day2.ps1|割合|
|---|---|---|
|2016-05-01 00:00: 00.0000000|2016-05-02 00:00: 00.0000000|33.2298494510578|
|2016-05-01 00:00: 00.0000000|2016-05-03 00:00: 00.0000000|16.9773830213667|
|2016-05-02 00:00: 00.0000000|2016-05-03 00:00: 00.0000000|14.5160020350006|

> [!NOTE] 
> 関数のエラーにより、クエリの結果が100% 正確ではありません `hll` 。 (エラーの詳細については、「」を参照してください [`dcount()`](dcount-aggfunction.md) 。
