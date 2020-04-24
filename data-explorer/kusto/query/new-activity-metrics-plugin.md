---
title: new_activity_metricsプラグイン - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーnew_activity_metricsプラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 0aad1c91fec6855030544596a08818b80bbf3d18
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512208"
---
# <a name="new_activity_metrics-plugin"></a>new_activity_metrics プラグイン

のコホートに対する有用なアクティビティメトリック (個別のカウント値、新しい値の個別のカウント、保持率、`New Users`およびチャーン率) を計算します。 (タイムウィンドウで最初`New Users`に見られたすべてのユーザー)の各コホートは、すべての以前のコホートと比較されます。 比較では、以前*のすべての時間枠が*考慮されます。 たとえば、from=T2 および to=T3 のレコードでは、T1 と T2 の両方で見られなかった T3 のすべてのユーザーが、ユーザーの個別の数になります。 
```kusto
T | evaluate new_activity_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**構文**

*T* `| evaluate` `,` *Start* `,` `,` *Window* `,` `,` `,` *IdColumn* `,` `,` *dim2* *End* *TimelineColumn* *Cohort* *dim1* IdColumn タイムライン列開始ウィンドウ [ コホート ] [ dim1 dim2 ..] `new_activity_metrics(`[`,` *ルックバック*]`)`

**引数**

* *T*: 入力表形式の式。
* *IdColumn*: ユーザー アクティビティを表す ID 値を持つ列の名前。 
* *タイムライン列*: タイムラインを表す列の名前。
* *開始*: 分析開始期間の値を持つスカラー。
* *終了*: 分析終了期間の値を持つスカラー。
* *ウィンドウ*: 分析ウィンドウ期間の値を持つスカラー。 数値/日時/タイムスタンプ値、またはの 1 つである[startofweek](startofweekfunction.md)/[startofmonth](startofmonthfunction.md)/[startofyear](startofyearfunction.md)`week`/`month`/`year`文字列を指定できます。 
* *コホート*: (オプション) 特定のコホートを示すスカラー定数。 指定しない場合、解析時間ウィンドウに対応するすべてのコホートが計算されて返されます。
* *dim1*、 *dim2*、 .: (オプション) アクティビティ指標の計算をスライスするディメンション列のリスト。
* *ルックバック*: (オプション) 'バックバック' 期間に属する ID のセットを持つ表形式の式

**戻り値**

'from' と 'to' のタイムライン期間の各組み合わせと既存のディメンションの組み合わせごとに、個別のカウント値、新しい値の個別のカウント、保持率、およびチャーン レートを持つテーブルを返します。

出力テーブルスキーマは次のとおりです。

|from_TimelineColumn|to_TimelineColumn|dcount_new_values|dcount_retained_values|dcount_churn_values|retention_rate|churn_rate|薄暗い1|..|dim_n|
|---|---|---|---|---|---|---|---|---|---|
|タイプ:*タイムライン列の時点*|同じ|long|long|double|double|double|..|..|..|

* `from_TimelineColumn`- 新しいユーザーのコホート。 このレコードのメトリックは、この期間に最初に表示されたすべてのユーザーを参照します。 *最初に見た*決定は、分析期間内の過去の期間をすべて考慮に入れます。 
* `to_TimelineColumn`- 比較される期間。 
* `dcount_new_values`- 以前とを含むすべての`to_TimelineColumn``from_TimelineColumn`*期間に*見られなかった個別のユーザーの数。 
* `dcount_retained_values`- すべての新規ユーザーのうち、最初に`from_TimelineColumn`見られる、で見られた個別のユーザーの数`to_TimelineCoumn`.
* `dcount_churn_values`- すべての新規ユーザーのうち、最初に`from_TimelineColumn`見られる、で見*られなかった*個別のユーザーの数. `to_TimelineCoumn`
* `retention_rate`- コホー`dcount_retained_values`トの外の割合 (ユーザーが最初に`from_TimelineColumn`で見た).
* `churn_rate`- コホー`dcount_churn_values`トの外の割合 (ユーザーが最初に`from_TimelineColumn`で見た).

**メモ**

および の`Retention Rate`定義`Churn Rate`については、 - プラグインのドキュメントの **「メモ**」[セクションactivity_metrics](./activity-metrics-plugin.md)参照してください。


**使用例**

次のサンプル データ セットは、どのユーザーがどの日に表示されたかを示しています。 テーブルは、ソース`Users`テーブルに基づいて次のように生成されました。 

```kusto
Users | summarize tostring(make_set(user)) by bin(Timestamp, 1d) | order by Timestamp asc;
```

|Timestamp|set_user|
|---|---|
|2019-11-01 00:00:00.0000000|[0,2,3,4]|
|2019-11-02 00:00:00.0000000|[0,1,3,4,5]|
|2019-11-03 00:00:00.0000000|[0,2,4,5]|
|2019-11-04 00:00:00.0000000|[0,1,2,3]|
|2019-11-05 00:00:00.0000000|[0,1,2,3,4]|

元のテーブルのプラグインの出力は次のとおりです。 

```kusto
let StartDate = datetime(2019-11-01 00:00:00);
let EndDate = datetime(2019-11-07 00:00:00);
Users 
| evaluate new_activity_metrics(user, Timestamp, StartDate, EndDate-1tick, 1d) 
| where from_Timestamp < datetime(2019-11-03 00:00:00.0000000)
```

|R|from_Timestamp|to_Timestamp|dcount_new_values|dcount_retained_values|dcount_churn_values|retention_rate|churn_rate|
|---|---|---|---|---|---|---|---|
|1|2019-11-01 00:00:00.0000000|2019-11-01 00:00:00.0000000|4|4|0|1|0|
|2|2019-11-01 00:00:00.0000000|2019-11-02 00:00:00.0000000|2|3|1|0.75|0.25|
|3|2019-11-01 00:00:00.0000000|2019-11-03 00:00:00.0000000|1|3|1|0.75|0.25|
|4|2019-11-01 00:00:00.0000000|2019-11-04 00:00:00.0000000|1|3|1|0.75|0.25|
|5|2019-11-01 00:00:00.0000000|2019-11-05 00:00:00.0000000|1|4|0|1|0|
|6|2019-11-01 00:00:00.0000000|2019-11-06 00:00:00.0000000|0|0|4|0|1|
|7|2019-11-02 00:00:00.0000000|2019-11-02 00:00:00.0000000|2|2|0|1|0|
|8|2019-11-02 00:00:00.0000000|2019-11-03 00:00:00.0000000|0|1|1|0.5|0.5|
|9|2019-11-02 00:00:00.0000000|2019-11-04 00:00:00.0000000|0|1|1|0.5|0.5|
|10|2019-11-02 00:00:00.0000000|2019-11-05 00:00:00.0000000|0|1|1|0.5|0.5|
|11|2019-11-02 00:00:00.0000000|2019-11-06 00:00:00.0000000|0|0|2|0|1|

以下は、出力からいくつかのレコードの分析です。 
* レコード`R=3``from_TimelineColumn` = , `2019-11-01` `to_TimelineColumn`  =  `2019-11-03`, :
    * このレコードで考慮されたユーザーは、11/1 に表示されるすべての新規ユーザーです。 これは最初の期間なので、これらはすべてのユーザーがそのビンにある – [0,2,3,4]
    * `dcount_new_values`– 11/1 で見られなかった 11/3 のユーザーの数. これには、単一のユーザー`5`が含まれます 。 
    * `dcount_retained_values`– 11/1 のすべての新規ユーザーのうち、11/3 まで保持された数はいくつですか? 3 つある`[0,2,4]`( `count_churn_values` ) が`3`1 つ (user= ) です。 
    * `retention_rate`= 0.75 – 11/1で最初に見られた4人の新規ユーザーのうち、3人の保持ユーザー。 

* レコード`R=9``from_TimelineColumn` = , `2019-11-02` `to_TimelineColumn`  =  `2019-11-04`, :
    * このレコードは、11/2 で最初に見られた新しいユーザーに焦点`1`を`5`当てています - ユーザーと . 
    * `dcount_new_values`– すべての期間を通して見られなかった11/4のユーザーの数`T0 .. from_Timestamp`. つまり、11/4 で見られるが、11/1 または 11/2 で見られなかったユーザーは、そのようなユーザーはいません。 
    * `dcount_retained_values`– 11/2 ( )`[1,5]`のすべての新規ユーザーのうち、11/4まで保持された数は何ですか? そのようなユーザー ( )`[1]`が 1 つあり、count_churn_valuesは 1 つ (ユーザー `5`) です。 
    * `retention_rate`は 0.5 – 11/2 の 2 つの新しいユーザーのうち 11/4 に保持された単一ユーザーです。 


### <a name="weekly-retention-rate-and-churn-rate-single-week"></a>週単位のリテンション率とチャーン率(1週間)

次のクエリでは、コホート (最初の週に到着したユーザー)`New Users`の週の 1 週間のウィンドウの保持率とチャーン率を計算します。

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
|2017-05-01 00:00:00.0000000|2017-05-01 00:00:00.0000000|1|0|
|2017-05-01 00:00:00.0000000|2017-05-08 00:00:00.0000000|0.544632768361582|0.455367231638418|
|2017-05-01 00:00:00.0000000|2017-05-15 00:00:00.0000000|0.031638418079096|0.968361581920904|
|2017-05-01 00:00:00.0000000|2017-05-22 00:00:00.0000000|0|1|
|2017-05-01 00:00:00.0000000|2017-05-29 00:00:00.0000000|0|1|


### <a name="weekly-retention-rate-and-churn-rate-complete-matrix"></a>週単位の保持率とチャーン率 (完全なマトリックス)

次のクエリでは、コホートの週の 1 週間のウィンドウ`New Users`の保持率とチャーン率を計算します。 前の例で 1 週間の統計を計算した場合、以下の例では、From/to の組み合わせごとに NxN テーブルが生成されます。

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
|2017-05-01 00:00:00.0000000|2017-05-01 00:00:00.0000000|1|0|
|2017-05-01 00:00:00.0000000|2017-05-08 00:00:00.0000000|0.190397350993377|0.809602649006622|
|2017-05-01 00:00:00.0000000|2017-05-15 00:00:00.0000000|0|1|
|2017-05-01 00:00:00.0000000|2017-05-22 00:00:00.0000000|0|1|
|2017-05-01 00:00:00.0000000|2017-05-29 00:00:00.0000000|0|1|
|2017-05-08 00:00:00.0000000|2017-05-08 00:00:00.0000000|1|0|
|2017-05-08 00:00:00.0000000|2017-05-15 00:00:00.0000000|0.405263157894737|0.594736842105263|
|2017-05-08 00:00:00.0000000|2017-05-22 00:00:00.0000000|0.227631578947368|0.772368421052632|
|2017-05-08 00:00:00.0000000|2017-05-29 00:00:00.0000000|0|1|
|2017-05-15 00:00:00.0000000|2017-05-15 00:00:00.0000000|1|0|
|2017-05-15 00:00:00.0000000|2017-05-22 00:00:00.0000000|0.785488958990536|0.214511041009464|
|2017-05-15 00:00:00.0000000|2017-05-29 00:00:00.0000000|0.237644584647739|0.762355415352261|
|2017-05-22 00:00:00.0000000|2017-05-22 00:00:00.0000000|1|0|
|2017-05-22 00:00:00.0000000|2017-05-29 00:00:00.0000000|0.621835443037975|0.378164556962025|
|2017-05-29 00:00:00.0000000|2017-05-29 00:00:00.0000000|1|0|


### <a name="weekly-retention-rate-with-lookback-period"></a>ルックバック期間を含む週単位の保存率

次のクエリでは、コホートの`New Users`定義に使用される ID`lookback`のセットを含む表形式のクエリ (このセットに表示されない`New Users`ID は 、 ) の期間を考慮した場合の`New Users`コホートの保持率が計算されます。 クエリは、分析期間中の 保持`New Users`動作を調べます。

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
|2017-05-01 00:00:00.0000000|2017-05-01 00:00:00.0000000|1|
|2017-05-01 00:00:00.0000000|2017-05-08 00:00:00.0000000|0.404081632653061|
|2017-05-01 00:00:00.0000000|2017-05-15 00:00:00.0000000|0.257142857142857|
|2017-05-01 00:00:00.0000000|2017-05-22 00:00:00.0000000|0.296326530612245|
|2017-05-01 00:00:00.0000000|2017-05-29 00:00:00.0000000|0.0587755102040816|
