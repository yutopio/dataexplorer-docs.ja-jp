---
title: new_activity_metrics プラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの new_activity_metrics プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: b376afda0874fdb70934ffc6861192ef9028e9aa
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347087"
---
# <a name="new_activity_metrics-plugin"></a>new_activity_metrics プラグイン

のコーホートの便利なアクティビティメトリック (個別のカウント値、個別の値の数、保持率、およびチャーン率) を計算し `New Users` ます。 各コーホート `New Users` (時間枠で最初に表示されたすべてのユーザー) は、以前のすべてのコーホートと比較されます。 比較では、以前の*すべて*の時間ウィンドウが考慮されます。 たとえば、from = T2、to = T3 のレコードでは、ユーザーの個別のカウントは、T1 と T2 の両方で見られなかった T3 のすべてのユーザーになります。 
```kusto
T | evaluate new_activity_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

## <a name="syntax"></a>構文

*T* `| evaluate` `new_activity_metrics(` *idcolumn* `,` *TimelineColumn* `,` *Start* `,` *終了* `,` *ウィンドウ*[ `,` *cohort*] [ `,` *dim1* `,` *dim2* `,` ...] [ `,` *元に戻す*]`)`

## <a name="arguments"></a>引数

* *T*: 入力テーブル式。
* *Idcolumn*: ユーザーアクティビティを表す ID 値を持つ列の名前。 
* *TimelineColumn*: タイムラインを表す列の名前。
* *Start*: 分析の開始期間の値を使用したスカラー。
* *End*: 分析終了期間の値を使用したスカラー。
* *ウィンドウ*: 分析ウィンドウ期間の値を持つスカラー。 には、数値/日付/時刻/タイムスタンプ値、またはのいずれかの文字列を指定でき `week` / `month` / `year` ます。この場合、すべての期間は、それに応じて[startofweek](startofweekfunction.md) / [startofmonth](startofmonthfunction.md) / [startofyear](startofyearfunction.md)になります。 
* *Cohort*: (省略可能) 特定の cohort を示すスカラー定数。 指定しない場合、分析時間枠に対応するすべてのコーホートが計算されて返されます。
* *dim1*、 *dim2*、...: (省略可能) アクティビティメトリックスの計算をスライスするディメンション列の一覧です。
* ルック*バック*: (省略可能) ' 参照元 ' 期間に属する id のセットを含む表形式の式

## <a name="returns"></a>戻り値

"From" と "to" の各タイムラインの期間と、既存の各ディメンションの組み合わせについて、個別のカウント値、新しい値の個別のカウント、保持率、およびチャーン率を含むテーブルを返します。

出力テーブルスキーマは次のとおりです。

|from_TimelineColumn|to_TimelineColumn|dcount_new_values|dcount_retained_values|dcount_churn_values|retention_rate|churn_rate|dim1|..|dim_n|
|---|---|---|---|---|---|---|---|---|---|
|型: as of *TimelineColumn*|同じ|long|long|double|double|double|..|..|..|

* `from_TimelineColumn`-新しいユーザーのコーホート。 このレコードのメトリックは、この期間内に最初に表示されたすべてのユーザーを指します。 *最初*に確認された決定は、分析期間内の以前のすべての期間を考慮します。 
* `to_TimelineColumn`-比較する期間。 
* `dcount_new_values`- `to_TimelineColumn` より前の*すべて*の期間に表示されなかった個別のユーザーの数 `from_TimelineColumn` 。 
* `dcount_retained_values`-最初に表示されたすべての新しいユーザーのうち、に `from_TimelineColumn` 表示された個別のユーザーの数 `to_TimelineCoumn` 。
* `dcount_churn_values`-で最初に表示されたすべての新しいユーザーのうち、 `from_TimelineColumn` で見ら*not*れなかった個別のユーザーの数 `to_TimelineCoumn` 。
* `retention_rate`- `dcount_retained_values` コーホートの外の割合 (ユーザーはに最初に表示さ `from_TimelineColumn` れます)。
* `churn_rate`- `dcount_churn_values` コーホートの外の割合 (ユーザーはに最初に表示さ `from_TimelineColumn` れます)。

**メモ**

の定義については、 `Retention Rate` `Churn Rate` [activity_metrics プラグイン](./activity-metrics-plugin.md)のドキュメントの「**メモ**」セクションを参照してください。


## <a name="examples"></a>例

次のサンプルデータセットは、どのユーザーがどの日に表示されたかを示しています。 テーブルは、次のように、ソーステーブルに基づいて生成されました `Users` 。 

```kusto
Users | summarize tostring(make_set(user)) by bin(Timestamp, 1d) | order by Timestamp asc;
```

|Timestamp|set_user|
|---|---|
|2019-11-01 00:00: 00.0000000|[0, 2, 3, 4]|
|2019-11-02 00:00: 00.0000000|[0, 1, 3, 4, 5]|
|2019-11-03 00:00: 00.0000000|[0, 2, 4, 5]|
|2019-11-04 00:00: 00.0000000|[0, 1, 2, 3]|
|2019-11-05 00:00: 00.0000000|[0, 1, 2, 3, 4]|

元のテーブルのプラグインの出力は次のようになります。 

```kusto
let StartDate = datetime(2019-11-01 00:00:00);
let EndDate = datetime(2019-11-07 00:00:00);
Users 
| evaluate new_activity_metrics(user, Timestamp, StartDate, EndDate-1tick, 1d) 
| where from_Timestamp < datetime(2019-11-03 00:00:00.0000000)
```

|R|from_Timestamp|to_Timestamp|dcount_new_values|dcount_retained_values|dcount_churn_values|retention_rate|churn_rate|
|---|---|---|---|---|---|---|---|
|1|2019-11-01 00:00: 00.0000000|2019-11-01 00:00: 00.0000000|4|4|0|1|0|
|2|2019-11-01 00:00: 00.0000000|2019-11-02 00:00: 00.0000000|2|3|1|0.75|0.25|
|3|2019-11-01 00:00: 00.0000000|2019-11-03 00:00: 00.0000000|1|3|1|0.75|0.25|
|4|2019-11-01 00:00: 00.0000000|2019-11-04 00:00: 00.0000000|1|3|1|0.75|0.25|
|5|2019-11-01 00:00: 00.0000000|2019-11-05 00:00: 00.0000000|1|4|0|1|0|
|6|2019-11-01 00:00: 00.0000000|2019-11-06 00:00: 00.0000000|0|0|4|0|1|
|7|2019-11-02 00:00: 00.0000000|2019-11-02 00:00: 00.0000000|2|2|0|1|0|
|8|2019-11-02 00:00: 00.0000000|2019-11-03 00:00: 00.0000000|0|1|1|0.5|0.5|
|9|2019-11-02 00:00: 00.0000000|2019-11-04 00:00: 00.0000000|0|1|1|0.5|0.5|
|10|2019-11-02 00:00: 00.0000000|2019-11-05 00:00: 00.0000000|0|1|1|0.5|0.5|
|11|2019-11-02 00:00: 00.0000000|2019-11-06 00:00: 00.0000000|0|0|2|0|1|

出力からのいくつかのレコードを分析するには、次のようにします。 
* レコード `R=3` 、 `from_TimelineColumn`  =  `2019-11-01` 、 `to_TimelineColumn`  =  `2019-11-03` :
    * このレコードに対して考慮されたユーザーは、11/1 に表示されるすべての新しいユーザーです。 これは最初の期間であるため、bin のすべてのユーザー– [0, 2, 3, 4]
    * `dcount_new_values`–11/1 で見られなかった11/3 のユーザーの数。 これには、1人のユーザー () が含まれ `5` ます。 
    * `dcount_retained_values`-11/1 のすべての新規ユーザーのうち、11/3 まで保持されたのはどれくらいですか? は3つ ( `[0,2,4]` ) ですが、 `count_churn_values` は 1 (user = `3` ) です。 
    * `retention_rate`= 0.75 –11/1 で最初に表示された4つの新しいユーザーのうち、3人のユーザーが保持しています。 

* レコード `R=9` 、 `from_TimelineColumn`  =  `2019-11-02` 、 `to_TimelineColumn`  =  `2019-11-04` :
    * このレコードは、11/2 – users and で最初に表示された新しいユーザーに焦点を当てて `1` `5` います。 
    * `dcount_new_values`–すべての期間に見られなかった11/4 のユーザーの数 `T0 .. from_Timestamp` 。 つまり、11/4 であっても、11/1 または11/2 には表示されていなかったユーザーは存在しません。 
    * `dcount_retained_values`– 11/2 () のすべての新規ユーザーのうち `[1,5]` 、11/4 まで保持された日数 このようなユーザー () が1つあり `[1]` ますが、count_churn_values は1つ (ユーザー `5` ) です。 
    * `retention_rate`0.5 –11/2 の2つの新しいユーザーのうち、11/4 に保持されていた1人のユーザー。 


### <a name="weekly-retention-rate-and-churn-rate-single-week"></a>週単位のリテンション率とチャーン率 (1 週間)

次のクエリでは、 `New Users` コーホート (最初の週に到着したユーザー) の週の1週間分の期間の保有期間とチャーン率を計算します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end  step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

|from_Day|to_Day|retention_rate|churn_rate|
|---|---|---|---|
|2017-05-01 00:00: 00.0000000|2017-05-01 00:00: 00.0000000|1|0|
|2017-05-01 00:00: 00.0000000|2017-05-08 00:00: 00.0000000|0.544632768361582|0.455367231638418|
|2017-05-01 00:00: 00.0000000|2017-05-15 00:00: 00.0000000|0.031638418079096|0.968361581920904|
|2017-05-01 00:00: 00.0000000|2017-05-22 00:00: 00.0000000|0|1|
|2017-05-01 00:00: 00.0000000|2017-05-29 00:00: 00.0000000|0|1|


### <a name="weekly-retention-rate-and-churn-rate-complete-matrix"></a>週単位の保持率とチャーン率 (マトリックス全体)

次のクエリでは、cohort の週の1週間分の期間のリテンション期間とチャーン率を計算し `New Users` ます。 前の例で1週間の統計を計算した場合、次の例では、from/to の組み合わせごとに NxN テーブルを生成します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end  step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Last parameter is omitted - 
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d)
| project from_Day, to_Day, retention_rate, churn_rate
```

|from_Day|to_Day|retention_rate|churn_rate|
|---|---|---|---|
|2017-05-01 00:00: 00.0000000|2017-05-01 00:00: 00.0000000|1|0|
|2017-05-01 00:00: 00.0000000|2017-05-08 00:00: 00.0000000|0.190397350993377|0.809602649006622|
|2017-05-01 00:00: 00.0000000|2017-05-15 00:00: 00.0000000|0|1|
|2017-05-01 00:00: 00.0000000|2017-05-22 00:00: 00.0000000|0|1|
|2017-05-01 00:00: 00.0000000|2017-05-29 00:00: 00.0000000|0|1|
|2017-05-08 00:00: 00.0000000|2017-05-08 00:00: 00.0000000|1|0|
|2017-05-08 00:00: 00.0000000|2017-05-15 00:00: 00.0000000|0.405263157894737|0.594736842105263|
|2017-05-08 00:00: 00.0000000|2017-05-22 00:00: 00.0000000|0.227631578947368|0.772368421052632|
|2017-05-08 00:00: 00.0000000|2017-05-29 00:00: 00.0000000|0|1|
|2017-05-15 00:00: 00.0000000|2017-05-15 00:00: 00.0000000|1|0|
|2017-05-15 00:00: 00.0000000|2017-05-22 00:00: 00.0000000|0.785488958990536|0.214511041009464|
|2017-05-15 00:00: 00.0000000|2017-05-29 00:00: 00.0000000|0.237644584647739|0.762355415352261|
|2017-05-22 00:00: 00.0000000|2017-05-22 00:00: 00.0000000|1|0|
|2017-05-22 00:00: 00.0000000|2017-05-29 00:00: 00.0000000|0.621835443037975|0.378164556962025|
|2017-05-29 00:00: 00.0000000|2017-05-29 00:00: 00.0000000|1|0|


### <a name="weekly-retention-rate-with-lookback-period"></a>ルックバック期間による週単位のリテンション率

次のクエリでは、 `New Users` 時間を考慮した場合のコーホートのリテンション率を計算し `lookback` ます。コーホートの定義に使用される一連の id を持つ表形式のクエリ `New Users` (このセットには表示されません `New Users` )。 クエリは、分析期間中のの保持動作を調べ `New Users` ます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Generate random data of user activities
let _lookback = datetime(2017-02-01);
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
let _data = range Day from _lookback to _end  step 1d
| extend d = tolong((Day - _lookback)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000;
//
let lookback_data = _data | where Day < _start | project Day, id;
_data
| evaluate new_activity_metrics(id, Day, _start, _end, 7d, _start, lookback_data)
| project from_Day, to_Day, retention_rate
```

|from_Day|to_Day|retention_rate|
|---|---|---|
|2017-05-01 00:00: 00.0000000|2017-05-01 00:00: 00.0000000|1|
|2017-05-01 00:00: 00.0000000|2017-05-08 00:00: 00.0000000|0.404081632653061|
|2017-05-01 00:00: 00.0000000|2017-05-15 00:00: 00.0000000|0.257142857142857|
|2017-05-01 00:00: 00.0000000|2017-05-22 00:00: 00.0000000|0.296326530612245|
|2017-05-01 00:00: 00.0000000|2017-05-29 00:00: 00.0000000|0.0587755102040816|
