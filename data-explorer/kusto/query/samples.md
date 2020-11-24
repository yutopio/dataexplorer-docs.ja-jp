---
title: Azure データエクスプローラーおよび Azure Monitor でのクエリのサンプル
description: この記事では、Azure データエクスプローラーおよび Azure Monitor の Kusto クエリ言語を使用する一般的なクエリと例について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/08/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 866df577fa039e8b92c31753197cf4a4fa03f139
ms.sourcegitcommit: faa747df81c49b96d173dbd5a28d2ca4f3a2db5f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "95783520"
---
# <a name="samples-for-queries-for-azure-data-explorer-and-azure-monitor"></a>Azure データエクスプローラーと Azure Monitor のクエリのサンプル

::: zone pivot="azuredataexplorer"

この記事では、Azure データエクスプローラーの一般的なクエリのニーズと、Kusto クエリ言語を使用してそれらを満たす方法について説明します。

## <a name="display-a-column-chart"></a>縦棒グラフを表示する

2つ以上の列を射影し、グラフの x 軸と y 軸として列を使用するには、次のように入力します。

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* 最初の列は x 軸を形成します。 数値、日付/時刻、または文字列を指定できます。 
* `where`、、およびを使用し `summarize` て、 `top` 表示するデータの量を制限します。
* 結果を並べ替えて x 軸の順序を定義します。

:::image type="content" source="images/samples/color-bar-chart.png" alt-text="10の位置のそれぞれの値を表す10色の列を含む縦棒グラフのスクリーンショット。":::

## <a name="get-sessions-from-start-and-stop-events"></a>開始および停止イベントからのセッションの取得

イベントのログでは、一部のイベントは、拡張されたアクティビティまたはセッションの開始または終了をマークします。 

|名前|City|SessionId|Timestamp|
|---|---|---|---|
|開始|London|2817330|2015-12-09T10:12:02.32|
|Game|London|2817330|2015-12-09T10:12:52.45|
|開始|Manchester|4267667|2015-12-09T10:14:02.23|
|Stop|London|2817330|2015-12-09T10:23:43.18|
|キャンセル|Manchester|4267667|2015-12-09T10:27:26.29|
|Stop|Manchester|4267667|2015-12-09T10:28:31.72|

すべてのイベントには、セッション ID () があり `SessionId` ます。 この課題は、開始イベントと停止イベントをセッション ID に一致させることです。

例:

```kusto
let Events = MyLogTable | where ... ;

Events
| where Name == "Start"
| project Name, City, SessionId, StartTime=timestamp
| join (Events 
        | where Name="Stop"
        | project StopTime=timestamp, SessionId) 
    on SessionId
| project City, SessionId, StartTime, StopTime, Duration = StopTime - StartTime
```

Start イベントと stop イベントをセッション ID と照合するには、次のようにします。

1. [Let](./letstatement.md)を使用して、結合を開始する前に、可能な限り減らすダウンするテーブルのプロジェクションに名前を付けます。
1. 開始時刻と停止時刻の両方が結果に表示されるように、 [project](./projectoperator.md) を使用してタイムスタンプの名前を変更します。 `project` また、結果に表示する他の列も選択します。 
1. [Join](./joinoperator.md)を使用して、同じアクティビティの開始エントリと停止エントリを一致させます。 アクティビティごとに1つの行が作成されます。 
1. `project`アクティビティの期間を示す列を追加するには、もう一度を使用します。

出力は次のようになります。

|City|SessionId|StartTime|StopTime|Duration|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Manchester|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

## <a name="get-sessions-without-using-a-session-id"></a>セッション ID を使用せずにセッションを取得する

Start イベントと stop イベントには、一致させることのできるセッション ID がないとします。 しかし、セッションが実行されたクライアントの IP アドレスがあります。 各クライアントアドレスで一度に1つのセッションのみを実行すると、各開始イベントを同じ IP アドレスから次の停止イベントに一致させることができます。

例:

```kusto
Events 
| where Name == "Start" 
| project City, ClientIp, StartTime = timestamp
| join  kind=inner
    (Events
    | where Name == "Stop" 
    | project StopTime = timestamp, ClientIp)
    on ClientIp
| extend duration = StopTime - StartTime 
    // Remove matches with earlier stops:
| where  duration > 0  
    // Pick out the earliest stop for each start and client:
| summarize arg_min(duration, *) by bin(StartTime,1s), ClientIp
```

は、すべての `join` 開始時刻を、同じクライアント IP アドレスからのすべての停止時刻と照合します。 サンプルコードは次のとおりです。

- 以前の停止時刻と一致する項目を削除します。
- 各セッションのグループを取得するには、[開始時刻] と [IP アドレス] でグループ化します。 
- `bin`パラメーターの関数を提供 `StartTime` します。 この手順を実行しない場合、Kusto は、開始時刻と間違った停止時間に一致する1時間のビンを自動的に使用します。

`arg_min` 各グループの最小期間を含む行を検索し `*` ます。パラメーターは、他のすべての列を通過します。 

`min_`各列名への引数プレフィックス。 

:::image type="content" source="images/samples/start-stop-ip-address-table.png" alt-text="[開始時刻]、[クライアント IP]、[期間]、[市区町村] の各列を含む、結果を一覧表示するテーブルのスクリーンショット。クライアント/開始時刻の組み合わせごとに停止します。"::: 

コードを追加して、簡単にサイズ設定されたビンで期間をカウントします。この例では、横棒グラフの設定により、をによって除算して、 `1s` タイムスパンを数値に変換します。

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/number-of-sessions-bar-chart.png" alt-text="セッションの数を示す縦棒グラフのスクリーンショット。指定された範囲に期間があります。":::

### <a name="full-example"></a>完全な例

```kusto
Logs  
| filter ActivityId == "ActivityId with Blablabla" 
| summarize max(Timestamp), min(Timestamp)  
| extend Duration = max_Timestamp - min_Timestamp 
 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"      
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedMaps > 0 
| summarize sum(TotalMapsSeconds) by UnitOfWorkId  
| extend JobMapsSeconds = sum_TotalMapsSeconds * 1 
| project UnitOfWorkId, JobMapsSeconds 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)   
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalReducesSeconds = ReducesSeconds / TotalLaunchedReducers 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedReducers > 0 
| summarize sum(TotalReducesSeconds) by UnitOfWorkId  
| extend JobReducesSeconds = sum_TotalReducesSeconds * 1 
| project UnitOfWorkId, JobReducesSeconds ) 
on UnitOfWorkId 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend JobName = extract("jobName=([^,]+),", 1, EventText)  
| extend StepName = extract("stepName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend LaunchTime = extract("launchTime=([^,]+),", 1, EventText, typeof(datetime))  
| extend FinishTime = extract("finishTime=([^,]+),", 1, EventText, typeof(datetime)) 
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps  
| extend TotalReducesSeconds = (ReducesSeconds / TotalLaunchedReducers / ReducesSeconds) * ReducesSeconds  
| extend CalculatedDuration = (TotalMapsSeconds + TotalReducesSeconds) * time(1s) 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2') 
on UnitOfWorkId 
| extend MapsFactor = TotalMapsSeconds / JobMapsSeconds 
| extend ReducesFactor = TotalReducesSeconds / JobReducesSeconds 
| extend CurrentLoad = 1536 + (768 * TotalLaunchedMaps) + (1536 * TotalLaunchedMaps) 
| extend NormalizedLoad = 1536 + (768 * TotalLaunchedMaps * MapsFactor) + (1536 * TotalLaunchedMaps * ReducesFactor) 
| summarize sum(CurrentLoad), sum(NormalizedLoad) by  JobName  
| extend SaveFactor = sum_NormalizedLoad / sum_CurrentLoad 
```

## <a name="chart-concurrent-sessions-over-time"></a>同時セッションの時系列グラフ

アクティビティのテーブルと、その開始時刻と終了時刻があるとします。 時間の経過と共に同時に実行されるアクティビティの数を表示するグラフを表示できます。

次に例を示し `X` ます。

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

1分間のグラフの場合は、実行中の各アクティビティを1分間隔でカウントします。

中間結果を以下に示します。

```kusto
X | extend samples = range(bin(StartTime, 1m), StopTime, 1m)
```

`range` は指定された間隔で値の配列を生成します。

|SessionId | StartTime | StopTime  | サンプル|
|---|---|---|---|
| a | 10:01:33 | 10:06:31 | [10:01:00,10:02:00,...10:06:00]|
| b | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
| c | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

これらの配列を保持するのではなく、 [mv-expand](./mvexpandoperator.md)を使用して拡張します。

```kusto
X | mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
```

|SessionId | StartTime | StopTime  | サンプル|
|---|---|---|---|
| a | 10:01:33 | 10:06:31 | 10:01:00|
| a | 10:01:33 | 10:06:31 | 10:02:00|
| a | 10:01:33 | 10:06:31 | 10:03:00|
| a | 10:01:33 | 10:06:31 | 10:04:00|
| a | 10:01:33 | 10:06:31 | 10:05:00|
| a | 10:01:33 | 10:06:31 | 10:06:00|
| b | 10:02:29 | 10:03:45 | 10:02:00|
| b | 10:02:29 | 10:03:45 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:04:00|

次に、結果をサンプル時間でグループ化し、各アクティビティの出現回数をカウントします。

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* 使用する `todatetime()` のは、 [mv-expand](./mvexpandoperator.md) の結果が動的な型の列であるためです。
* `bin()`数値および日付の場合、間隔を指定しないと、は常に既定の間隔を使用して関数を適用するため、を使用し `summarize` `bin()` ます。 

出力は次のようになります。

| count_SessionId | サンプル|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

棒グラフまたは線上を使用して、結果を表示できます。

## <a name="introduce-null-bins-into-summarize"></a>Null ビンを *概要* に導入する

`summarize`日付/時刻列で構成されるグループキーに演算子が適用されている場合は、それらの値を固定幅のビンにビン分割します。

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

この例では、 `T` 5 分間の各ビンに分類される、の行グループごとに1行のテーブルを生成します。

このコードでは、"null ビン" を追加しています。これは `StartTime` 、 `StopTime` に対応する行がないとの間の time bin 値の行です `T` 。 これらのビンにテーブルを "埋め込む" ことをお勧めします。これを行う1つの方法を次に示します。

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| summarize Count=count() by bin(Timestamp, 5m)
| where ...
| union ( // 1
  range x from 1 to 1 step 1 // 2
  | mv-expand Timestamp=range(StartTime, StopTime, 5m) to typeof(datetime) // 3
  | extend Count=0 // 4
  )
| summarize Count=sum(Count) by bin(Timestamp, 5m) // 5 
```

上記のクエリのステップバイステップの説明を次に示します。 

1. `union`テーブルに行を追加するには、演算子を使用します。 これらの行は、式によって生成され `union` ます。
1. 演算子は、 `range` 1 つの行と列を含むテーブルを生成します。 では、テーブルがでは使用されません `mv-expand` 。
1. `mv-expand`関数に対する演算子は、との `range` 間の5分のビンと同じ数の行を作成し `StartTime` `EndTime` ます。
1. のを `Count` 使用 `0` します。
1. 操作は、 `summarize` 元の引数 (left、outer) からにビンをグループ化し `union` ます。 また、演算子は内部引数からそれにビン分割します (null ビン行)。 このプロセスにより、出力には、0または元のカウントの値を持つビンごとに1つの行が含まれるようになります。

## <a name="get-more-from-your-data-by-using-kusto-with-machine-learning"></a>Kusto と machine learning を使用してデータをさらに活用する 

多くの興味深いユースケースでは、機械学習アルゴリズムを使用して、テレメトリデータから興味深い洞察を導き出します。 多くの場合、これらのアルゴリズムでは、入力として厳密に構造化されたデータセットが必要です。 通常、未加工のログデータは、必要な構造とサイズと一致しません。 

まず、特定の Bing 推論サービスのエラー率に異常がないか調べてください。 Logs テーブルには650億レコードがあります。 次の基本的なクエリでは、25万エラーをフィルター処理し、異常検出関数 [series_decompose_anomalies](series-decompose-anomaliesfunction.md)を使用するタイムシリーズのエラー数を作成します。 異常は Kusto サービスによって検出され、タイムシリーズグラフの赤い点として強調表示されます。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

サービスでは、疑わしいエラー率を持ついくつかの時間バケットが特定されました。 Kusto を使用して、この期間を拡大します。 次に、列を集計するクエリを実行し `Message` ます。 上位のエラーを検索してみてください。 

メッセージのスタックトレース全体の関連する部分が除去されるため、結果はページ上でより良くなります。 

上位8個のエラーが正しく識別されていることを確認できます。 ただし、次に示すのは、データの変更を含む書式指定文字列を使用してエラーメッセージが作成されたためです。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| summarize count() by Message 
| top 10 by count_ 
| project count_, Message 
```

|count_|Message
|---|---
|7125|メソッド ' RunCycleFromInterimData ' の ExecuteAlgorithmMethod に失敗しました...
|7125|InferenceHostService は、tem failed..Sys呼び出します。NullReferenceException: オブジェクト参照がオブジェクトのインスタンスに設定されていません...
|7124|予期しない推論システム error..System。NullReferenceException: オブジェクト参照がオブジェクトのインスタンスに設定されていません... 
|5112|予期しない推論システム error..System。NullReferenceException: オブジェクト参照がオブジェクトのインスタンスに設定されていません。
|174|InferenceHostService call failed..System. CommunicationException: パイプへの書き込み中にエラーが発生しました:...
|10|メソッド ' RunCycleFromInterimData ' の ExecuteAlgorithmMethod に失敗しました...
|10|推論システムエラー..UserInterimDataManagerException (..........
|3|InferenceHostService 呼び出し failed..System:...
|1|推論システムエラー..."%.......................AIS TraceId: 8292FC561AC64BED8FA243808FE74EFD...
|1|推論システムエラー..."%.......................AIS TraceId: 5F19F7587-71 3EC9B641E02E701AFBF...

この時点で、演算子を使用すると、が `reduce` 役立ちます。 演算子は、コード内の同じトレースインストルメンテーションポイントで発生した異なるエラー63を識別しました。 `reduce` では、その時間枠で意味のある追加のエラートレースに焦点を当てることができます。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| reduce by Message with threshold=0.35
| project Count, Pattern
```

|Count|Pattern
|---|---
|7125|メソッド ' RunCycleFromInterimData ' の ExecuteAlgorithmMethod に失敗しました...
|  7125|InferenceHostService は、tem failed..Sys呼び出します。NullReferenceException: オブジェクト参照がオブジェクトのインスタンスに設定されていません...
|  7124|予期しない推論システム error..System。NullReferenceException: オブジェクト参照がオブジェクトのインスタンスに設定されていません...
|  5112|予期しない推論システム error..System。NullReferenceException: オブジェクト参照がオブジェクトのインスタンスに設定されていません...
|  174|InferenceHostService call failed..System. CommunicationException: パイプへの書き込み中にエラーが発生しました:...
|  63|推論システムエラー..\*要求.: \* オブジェクトに書き込むための書き込み。: "%" というオブジェクトを書き込みます.. \* .
|  10|メソッド ' RunCycleFromInterimData ' の ExecuteAlgorithmMethod に失敗しました...
|  10|推論システムエラー..UserInterimDataManagerException (..........
|  3|InferenceHostService は、tem failed..Sys呼び出します。ServiceModel. \* : オブジェクト (system.servicemodel... \* + \* ) \* \* は \* ...Syst...

これで、検出された異常に起因する上位のエラーを確認することができます。

サンプルシステム間で発生したこれらのエラーの影響を理解するには、次の点を考慮してください。 
* テーブルには、 `Logs` やなどの追加の次元データが含まれてい `Component` `Cluster` ます。
* 新しい autocluster プラグインは、単純なクエリを使用してコンポーネントとクラスターの洞察を得るのに役立ちます。 

次の例では、上位4つのエラーはそれぞれコンポーネントに固有であることがわかります。 また、上位3つのエラーは DB4 クラスターに固有ですが、4番目のエラーはすべてのクラスターで発生します。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|Count |パーセンテージ (%)|コンポーネント|クラスター|Message
|---|---|---|---|---
|7125|26.64|InferenceHostService|DB4|メソッドの ExecuteAlgorithmMethod...
|7125|26.64|不明なコンポーネント|DB4|InferenceHostService の呼び出しに失敗しました...
|7124|26.64|InferenceAlgorithmExecutor|DB4|予期しない推論システムエラー...
|5112|19.11|InferenceAlgorithmExecutor|*|予期しない推論システムエラー...

## <a name="map-values-from-one-set-to-another"></a>あるセットから別のセットに値をマップする

一般的なクエリのユースケースは、値の静的マッピングです。 静的なマッピングを使用すると、結果の体裁を高めることができます。

たとえば、次の表では、は `DeviceModel` デバイスモデルを指定しています。 デバイスモデルの使用は、デバイス名を参照するのに便利な形式ではありません。  

|DeviceModel |Count 
|---|---
|iPhone5、1 |32 
|iPhone3、2 |432 
|iPhone7、2 |55 
|iPhone5、2 |66 

 フレンドリ名を使用する方が便利です。  

|FriendlyName |Count 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

次の2つの例は、デバイスモデルを使用してデバイスを識別するフレンドリ名に変更する方法を示しています。  

### <a name="map-by-using-a-dynamic-dictionary"></a>動的ディクショナリを使用してマップする

動的ディクショナリと動的アクセサーを使用してマッピングを実現できます。 次に例を示します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Dataset definition
let Source = datatable(DeviceModel:string, Count:long)
[
  'iPhone5,1', 32,
  'iPhone3,2', 432,
  'iPhone7,2', 55,
  'iPhone5,2', 66,
];
// Query start here
let phone_mapping = dynamic(
  {
    "iPhone5,1" : "iPhone 5",
    "iPhone3,2" : "iPhone 4",
    "iPhone7,2" : "iPhone 6",
    "iPhone5,2" : "iPhone5"
  });
Source 
| project FriendlyName = phone_mapping[DeviceModel], Count
```

|FriendlyName|Count|
|---|---|
|iPhone 5|32|
|iPhone 4|432|
|iPhone 6|55|
|iPhone5|66|

### <a name="map-by-using-a-static-table"></a>静的テーブルを使用してマップする

また、永続的なテーブルとオペレーターを使用してマッピングを行うこともでき `join` ます。
 
1. マッピングテーブルを作成するのは1回だけです。

    ```kusto
    .create table Devices (DeviceModel: string, FriendlyName: string) 

    .ingest inline into table Devices 
        ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
    ```

1. デバイスコンテンツのテーブルを作成します。

    |DeviceModel |FriendlyName 
    |---|---
    |iPhone5、1 |iPhone 5 
    |iPhone3、2 |iPhone 4 
    |iPhone7、2 |iPhone 6 
    |iPhone5、2 |iPhone5 

1. テストテーブルソースを作成します。

    ```kusto
    .create table Source (DeviceModel: string, Count: int)

    .ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
    ```

1. テーブルを結合し、プロジェクトを実行します。

   ```kusto
   Devices  
   | join (Source) on DeviceModel  
   | project FriendlyName, Count
   ```

出力は次のようになります。

|FriendlyName |Count 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 


## <a name="create-and-use-query-time-dimension-tables"></a>クエリ時間ディメンションテーブルを作成して使用する

多くの場合、データベースに格納されていないアドホックディメンションテーブルを使用してクエリの結果を結合する必要があります。 1つのクエリを対象とするテーブルを結果とする式を定義できます。 

次に例を示します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// Create a query-time dimension table using datatable
let DimTable = datatable(EventType:string, Code:string)
  [
    "Heavy Rain", "HR",
    "Tornado",    "T"
  ]
;
DimTable
| join StormEvents on EventType
| summarize count() by Code
```

もう少し複雑な例を次に示します。

```kusto
// Create a query-time dimension table using datatable
let TeamFoundationJobResult = datatable(Result:int, ResultString:string)
  [
    -1, 'None', 0, 'Succeeded', 1, 'PartiallySucceeded', 2, 'Failed',
    3, 'Stopped', 4, 'Killed', 5, 'Blocked', 6, 'ExtensionNotFound',
    7, 'Inactive', 8, 'Disabled', 9, 'JobInitializationError'
  ]
;
JobHistory
  | where PreciseTimeStamp > ago(1h)
  | where Service  != "AX"
  | where Plugin has "Analytics"
  | sort by PreciseTimeStamp desc
  | join kind=leftouter TeamFoundationJobResult on Result
  | extend ExecutionTimeSpan = totimespan(ExecutionTime)
  | project JobName, StartTime, ExecutionTimeSpan, ResultString, ResultMessage
```

## <a name="retrieve-the-latest-records-by-timestamp-per-identity"></a>Id ごとに (タイムスタンプによる) 最新のレコードを取得する

次のようなテーブルがあるとします。
* `ID`ユーザー ID やノード ID など、各行が関連付けられているエンティティを識別する列
* `timestamp`行の時間参照を提供する列
* 他の列

上位の入れ子に [なった演算子](topnestedoperator.md)を使用すると、列の各値について最新の2つのレコードを返すクエリを作成できます `ID` 。_最新_ のは、 _`timestamp` の最大値を持つ_ ように定義されています。

```kusto
datatable(id:string, timestamp:datetime, bla:string)           // #1
  [
  "Barak",  datetime(2015-01-01), "1",
  "Barak",  datetime(2016-01-01), "2",
  "Barak",  datetime(2017-01-20), "3",
  "Donald", datetime(2017-01-20), "4",
  "Donald", datetime(2017-01-18), "5",
  "Donald", datetime(2017-01-19), "6"
  ]
| top-nested   of id        by dummy0=max(1),                  // #2
  top-nested 2 of timestamp by dummy1=max(timestamp),          // #3
  top-nested   of bla       by dummy2=max(1)                   // #4
| project-away dummy0, dummy1, dummy2                          // #5
```


上記のクエリのステップバイステップの説明を次に示します (番号付けは、コードのコメント内の番号を示しています)。

1. は、 `datatable` デモンストレーション用のテストデータを生成する方法です。 通常、ここでは実際のデータを使用します。
1. この行は基本的 _に、のすべての `id` 個別の値を返すことを_ 意味します。 
1. 次に、を最大化する上位2つのレコードに対して、次の行が返されます。
     * `timestamp`列
     * 前のレベルの列 (ここではここでのみ `id` )
     * このレベルで指定された列 (ここでは `timestamp` )
1. この行は、 `bla` 前のレベルで返された各レコードの列の値を追加します。 テーブルに関心のある他の列がある場合は、これらの列ごとにこの行を繰り返すことができます。
1. 最後の行では、プロジェクト外の [演算子](projectawayoperator.md) を使用して、によって導入された "extra" 列を削除し `top-nested` ます。

## <a name="extend-a-table-by-a-percentage-of-the-total-calculation"></a>合計計算に対する割合でテーブルを拡張する

数値列を含む表形式の式は、全体に対する割合として値が指定されている場合に、ユーザーにとって便利です。

たとえば、クエリによって次のテーブルが生成されるとします。

|SomeSeries|Int|
|----------|-------|
|Apple       |    100|
|Banana       |    200|

次のようにテーブルを表示します。

|SomeSeries|Int|Pct |
|----------|-------|----|
|Apple       |    100|33.3|
|Banana       |    200|66.6|

テーブルの表示方法を変更するには、列の合計 (合計) を計算 `SomeInt` してから、この列の各値を合計で除算します。 任意の結果を得るには、 [as 演算子](asoperator.md)を使用します。

次に例を示します。

```kusto
// The following table literally represents a long calculation
// that ends up with an anonymous tabular value:
datatable (SomeInt:int, SomeSeries:string) [
  100, "Apple",
  200, "Banana",
]
// We now give this calculation a name ("X"):
| as X
// Having this name we can refer to it in the sub-expression
// "X | summarize sum(SomeInt)":
| extend Pct = 100 * bin(todouble(SomeInt) / toscalar(X | summarize sum(SomeInt)), 0.001)
```

## <a name="perform-aggregations-over-a-sliding-window"></a>スライディングウィンドウに対して集計を実行する

次の例では、スライディングウィンドウを使用して列を集計する方法を示します。 このクエリでは、次の表を使用します。このテーブルには、タイムスタンプによる果物の価格が含まれています。

7日間のスライディングウィンドウを使用して、各果物の1日あたりの最小、最大、および合計のコストを計算します。 結果セットの各レコードは、前の7日間を集計し、結果には分析期間の1日あたりのレコードが含まれます。

果物テーブル:

|Timestamp|Fruit|Price|
|---|---|---|
|2018-09-24 21:00: 00.0000000|バナナ|3|
|2018-09-25 20:00: 00.0000000|Apples|9|
|2018-09-26 03:00: 00.0000000|バナナ|4|
|2018-09-27 10:00: 00.0000000|Plums|8|
|2018-09-28 07:00: 00.0000000|バナナ|6|
|2018-09-29 21:00: 00.0000000|バナナ|8|
|2018-09-30 01:00: 00.0000000|Plums|2|
|2018-10-01 05:00: 00.0000000|バナナ|0|
|2018-10-02 02:00: 00.0000000|バナナ|0|
|2018-10-03 13:00: 00.0000000|Plums|4|
|2018-10-04 14:00: 00.0000000|Apples|8|
|2018-10-05 05:00: 00.0000000|バナナ|2|
|2018-10-06 08:00: 00.0000000|Plums|8|
|2018-10-07 12:00: 00.0000000|バナナ|0|

スライディングウィンドウの集計クエリを次に示します。 クエリ結果の後の説明を参照してください。

```kusto
let _start = datetime(2018-09-24);
let _end = _start + 13d; 
Fruits 
| extend _bin = bin_at(Timestamp, 1d, _start) // #1 
| extend _endRange = iif(_bin + 7d > _end, _end, 
                            iff( _bin + 7d - 1d < _start, _start,
                                iff( _bin + 7d - 1d < _bin, _bin,  _bin + 7d - 1d)))  // #2
| extend _range = range(_bin, _endRange, 1d) // #3
| mv-expand _range to typeof(datetime) limit 1000000 // #4
| summarize min(Price), max(Price), sum(Price) by Timestamp=bin_at(_range, 1d, _start) ,  Fruit // #5
| where Timestamp >= _start + 7d; // #6

```

出力は次のようになります。

|Timestamp|Fruit|min_Price|max_Price|sum_Price|
|---|---|---|---|---|
|2018-10-01 00:00: 00.0000000|Apples|9|9|9|
|2018-10-01 00:00: 00.0000000|バナナ|0|8|18|
|2018-10-01 00:00: 00.0000000|Plums|2|8|10|
|2018-10-02 00:00: 00.0000000|バナナ|0|8|18|
|2018-10-02 00:00: 00.0000000|Plums|2|8|10|
|2018-10-03 00:00: 00.0000000|Plums|2|8|14|
|2018-10-03 00:00: 00.0000000|バナナ|0|8|14|
|2018-10-04 00:00: 00.0000000|バナナ|0|8|14|
|2018-10-04 00:00: 00.0000000|Plums|2|4|6|
|2018-10-04 00:00: 00.0000000|Apples|8|8|8|
|2018-10-05 00:00: 00.0000000|バナナ|0|8|10|
|2018-10-05 00:00: 00.0000000|Plums|2|4|6|
|2018-10-05 00:00: 00.0000000|Apples|8|8|8|
|2018-10-06 00:00: 00.0000000|Plums|2|8|14|
|2018-10-06 00:00: 00.0000000|バナナ|0|2|2|
|2018-10-06 00:00: 00.0000000|Apples|8|8|8|
|2018-10-07 00:00: 00.0000000|バナナ|0|2|2|
|2018-10-07 00:00: 00.0000000|Plums|4|8|12|
|2018-10-07 00:00: 00.0000000|Apples|8|8|8|

このクエリでは、入力テーブル内の各レコードが実際の外観から7日後に "ストレッチ" (重複) されます。 各レコードは実際には7回表示されます。 その結果、日単位の集計には、過去7日間のすべてのレコードが含まれます。


上記のクエリのステップバイステップの説明を次に示します。 

1. 各レコードを1日 (からの相対) にビン分割 `_start` します。 
1. レコードごとの範囲の終わりを決定します。 `_bin + 7d` ただし、値がおよびの範囲外である場合は、 `_start` 調整さ `_end` れます。 
1. 各レコードについて、現在のレコードの日から始まる7日間の配列を作成します (タイムスタンプ)。 
1. `mv-expand` この配列は、各レコードを7つのレコードに複製します。 
1. 日ごとに集計関数を実行します。 #4 のため、このステップでは _過去_ 7 日間を実際に要約します。 
1. 最初の7日間は7日間のルックバック期間がないため、最初の7日間のデータは不完全です。 最初の7日間は、最終結果から除外されます。 この例では、2018-10-01 の集計にのみ参加します。

## <a name="find-the-preceding-event"></a>前のイベントを検索する

次の例では、2つのデータセット間で前のイベントを検索する方法を示します。  

A と B の2つのデータセットがあります。データセット B の各レコードについて、データセット A (つまり、B よりも前のレコード) でその前のイベントを検索 `arg_max` します。 _older_

サンプルデータセットを次に示します。 

```kusto
let A = datatable(Timestamp:datetime, ID:string, EventA:string)
[
    datetime(2019-01-01 00:00:00), "x", "Ax1",
    datetime(2019-01-01 00:00:01), "x", "Ax2",
    datetime(2019-01-01 00:00:02), "y", "Ay1",
    datetime(2019-01-01 00:00:05), "y", "Ay2",
    datetime(2019-01-01 00:00:00), "z", "Az1"
];
let B = datatable(Timestamp:datetime, ID:string, EventB:string)
[
    datetime(2019-01-01 00:00:03), "x", "B",
    datetime(2019-01-01 00:00:04), "x", "B",
    datetime(2019-01-01 00:00:04), "y", "B",
    datetime(2019-01-01 00:02:00), "z", "B"
];
A; B
```

|Timestamp|ID|EventB|
|---|---|---|
|2019-01-01 00:00: 00.0000000|x|Ax1|
|2019-01-01 00:00: 00.0000000|z|Az1|
|2019-01-01 00:00: 01.0000000|x|Ax2|
|2019-01-01 00:00: 02.0000000|○|Ay1|
|2019-01-01 00:00: 05.0000000|○|Ay2|

</br>

|Timestamp|ID|EventA|
|---|---|---|
|2019-01-01 00:00: 03.0000000|x|B|
|2019-01-01 00:00: 04.0000000|x|B|
|2019-01-01 00:00: 04.0000000|○|B|
|2019-01-01 00:02: 00.0000000|z|B|

予想される出力: 

|ID|Timestamp|EventB|A_Timestamp|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00: 03.0000000|B|2019-01-01 00:00: 01.0000000|Ax2|
|x|2019-01-01 00:00: 04.0000000|B|2019-01-01 00:00: 01.0000000|Ax2|
|○|2019-01-01 00:00: 04.0000000|B|2019-01-01 00:00: 02.0000000|Ay1|
|z|2019-01-01 00:02: 00.0000000|B|2019-01-01 00:00: 00.0000000|Az1|

この問題には、2つの異なる方法を使用することをお勧めします。 特定のデータセットで両方をテストして、シナリオに最適なものを見つけることができます。

> [!NOTE] 
> 各方法は、データセットによって異なる方法で実行される場合があります。

### <a name="approach-1"></a>方法1

この方法では、ID とタイムスタンプを使って両方のデータセットをシリアル化します。 次に、データセット B 内のすべてのイベントを、データセット A 内のすべてのイベントをグループ化します。最後に、 `arg_max` グループ内のデータセット A 内のすべてのイベントを取得します。

```kusto
A
| extend A_Timestamp = Timestamp, Kind="A"
| union (B | extend B_Timestamp = Timestamp, Kind="B")
| order by ID, Timestamp asc 
| extend t = iff(Kind == "A" and (prev(Kind) != "A" or prev(Id) != ID), 1, 0)
| extend t = row_cumsum(t)
| summarize Timestamp=make_list(Timestamp), EventB=make_list(EventB), arg_max(A_Timestamp, EventA) by t, ID
| mv-expand Timestamp to typeof(datetime), EventB to typeof(string)
| where isnotempty(EventB)
| project-away t
```

### <a name="approach-2"></a>方法2

この問題を解決するには、最大の復元期間が必要です。 この方法では、データセット A のレコードがデータセット B とどの程度 _古い_ かを確認します。次に、メソッドは、ID とこのルックバック期間に基づいて、2つのデータセットを結合します。

では、 `join` すべての候補が生成されます。すべてのデータセットには、データセット B のレコードよりも古いレコードと、元のデータが含まれています。 次に、データセット B に最も近いものがによってフィルター処理され `arg_min (TimestampB - TimestampA)` ます。 ルックバック期間が短くなるほど、クエリの結果が向上します。

次の例では、ルックバック期間がに設定されて `1m` います。 ID を持つレコードに `z` `A` は、 `A` イベントが2分が経過しているため、対応するイベントがありません。

```kusto 
let _maxLookbackPeriod = 1m;  
let _internalWindowBin = _maxLookbackPeriod / 2;
let B_events = B 
    | extend ID = new_guid()
    | extend _time = bin(Timestamp, _internalWindowBin)
    | extend _range = range(_time - _internalWindowBin, _time + _maxLookbackPeriod, _internalWindowBin) 
    | mv-expand _range to typeof(datetime) 
    | extend B_Timestamp = Timestamp, _range;
let A_events = A 
    | extend _time = bin(Timestamp, _internalWindowBin)
    | extend _range = range(_time - _internalWindowBin, _time + _maxLookbackPeriod, _internalWindowBin) 
    | mv-expand _range to typeof(datetime) 
    | extend A_Timestamp = Timestamp, _range;
B_events
    | join kind=leftouter (
        A_events
) on ID, _range
| where isnull(A_Timestamp) or (A_Timestamp <= B_Timestamp and B_Timestamp <= A_Timestamp + _maxLookbackPeriod)
| extend diff = coalesce(B_Timestamp - A_Timestamp, _maxLookbackPeriod*2)
| summarize arg_min(diff, *) by ID
| project ID, B_Timestamp, A_Timestamp, EventB, EventA
```

|ID|B_Timestamp|A_Timestamp|EventB|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00: 03.0000000|2019-01-01 00:00: 01.0000000|B|Ax2|
|x|2019-01-01 00:00: 04.0000000|2019-01-01 00:00: 01.0000000|B|Ax2|
|○|2019-01-01 00:00: 04.0000000|2019-01-01 00:00: 02.0000000|B|Ay1|
|z|2019-01-01 00:02: 00.0000000||B||


## <a name="next-steps"></a>次のステップ

- [Kusto クエリ言語に関するチュートリアル](tutorial.md?pivots=azuredataexplorer)を紹介します。

::: zone-end

::: zone pivot="azuremonitor"

この記事では、Azure Monitor での一般的なクエリのニーズと、Kusto クエリ言語を使用してそれらを満たす方法について説明します。

## <a name="string-operations"></a>文字列操作

以下のセクションでは、Kusto クエリ言語を使用する場合の文字列の操作方法の例を紹介します。

### <a name="strings-and-how-to-escape-them"></a>文字列とエスケープする方法

文字列値は、一重引用符または二重引用符でラップされます。 文字を \\ エスケープするために、文字の左側に円記号 () を追加します。タブの場合は、改行の場合は、単一引用符の場合は `\t` `\n` `\"` です。

```kusto
print "this is a 'string' literal in double \" quotes"
```

```kusto
print 'this is a "string" literal in single \' quotes'
```

"\\" がエスケープ文字として機能しないようにするには、文字列に "\@" をプレフィックスとして追加します。

```kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


### <a name="string-comparisons"></a>文字列の比較

演算子       |説明                         |大文字と小文字を区別する|例 (`true` になる)
---------------|------------------------------------|--------------|-----------------------
`==`           |等しい                              |はい           |`"aBc" == "aBc"`
`!=`           |等しくない                          |はい           |`"abc" != "ABC"`
`=~`           |等しい                              |いいえ            |`"abc" =~ "ABC"`
`!~`           |等しくない                          |いいえ            |`"aBc" !~ "xyz"`
`has`          |右側の値は、左側の値の完全な用語です。 |いいえ|`"North America" has "america"`
`!has`         |右辺値が左辺値の完全な用語ではありません       |いいえ            |`"North America" !has "amer"` 
`has_cs`       |右側の値は、左側の値の完全な用語です。 |はい|`"North America" has_cs "America"`
`!has_cs`      |右辺値が左辺値の完全な用語ではありません       |はい            |`"North America" !has_cs "amer"` 
`hasprefix`    |右側の値は、左辺値の用語プレフィックスです         |いいえ            |`"North America" hasprefix "ame"`
`!hasprefix`   |右辺値が左辺値の用語プレフィックスではありません     |いいえ            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |右側の値は、左辺値の用語プレフィックスです         |はい            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |右辺値が左辺値の用語プレフィックスではありません     |はい            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |右側の値は、左辺値の語句サフィックスです         |いいえ            |`"North America" hassuffix "ica"`
`!hassuffix`   |右辺値が左辺値の語句サフィックスではありません     |いいえ            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |右側の値は、左辺値の語句サフィックスです         |はい            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |右辺値が左辺値の語句サフィックスではありません     |はい            |`"North America" !hassuffix_cs "icA"`
`contains`     |右側の値は、左側の値のサブシーケンスとして発生します  |いいえ            |`"FabriKam" contains "BRik"`
`!contains`    |左側の値に右側の値がありません           |いいえ            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |右側の値は、左側の値のサブシーケンスとして発生します  |はい           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |左側の値に右側の値がありません           |はい           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |右側の値は左側の値の最初のサブシーケンスです|いいえ            |`"Fabrikam" startswith "fab"`
`!startswith`  |右側の値は、左側の値の最初のサブシーケンスではありません|いいえ        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |右側の値は左側の値の最初のサブシーケンスです|はい            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |右側の値は、左側の値の最初のサブシーケンスではありません|はい        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |右側の値は左側の値の終わりのサブシーケンスです|いいえ             |`"Fabrikam" endswith "Kam"`
`!endswith`    |右辺の値が左辺の値の終わりのサブシーケンスではありません|いいえ         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |右側の値は左側の値の終わりのサブシーケンスです|はい             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |右辺の値が左辺の値の終わりのサブシーケンスではありません|はい         |`"Fabrikam" !endswith "brik"`
`matches regex`|左側の値に右側の値との一致が含まれています|はい           |`"Fabrikam" matches regex "b.*k"`
`in`           |要素のいずれかに等しい       |はい           |`"abc" in ("123", "345", "abc")`
`!in`          |要素のいずれとも等しくない   |はい           |`"bca" !in ("123", "345", "abc")`


### <a name="countof"></a>*countof*

文字列内の部分文字列の出現回数をカウントします。 プレーン文字列に一致するか、正規表現 (regex) を使用できます。 文字列の正規表現は重複する可能性がありますが、一致する regex は重複しません。

```
countof(text, search [, kind])
```

- `text`: 入力文字列 
- `search`: テキスト内で一致するプレーン文字列または正規表現
- `kind`:_正規_  |  _表現_(既定値: 標準)。

コンテナー内で検索文字列が一致する回数を返します。 文字列の正規表現は重複する可能性がありますが、一致する regex は重複しません。

#### <a name="plain-string-matches"></a>プレーン文字列の一致

```kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>正規表現の一致

```kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


### <a name="extract"></a>*extract*

特定の文字列から正規表現への一致を取得します。 必要に応じて、抽出された部分文字列を指定した型に変換できます。

```kusto
extract(regex, captureGroup, text [, typeLiteral])
```

- `regex`: 正規表現。
- `captureGroup`: 抽出するキャプチャグループを示す正の整数定数。 一致した文字列全体に0を使用し、正規表現の最初のかっこと一致した値を1に、2つ以上のかっこを指定し \( \) ます。
- `text` -検索する文字列。
- `typeLiteral` -省略可能な型リテラル (例: `typeof(long)` )。 指定した場合、抽出された部分文字列はこの型に変換されます。

指定されたキャプチャグループに一致し、必要に応じてに変換された部分文字列を返し `captureGroup` `typeLiteral` ます。 一致するものがない場合、または型変換に失敗した場合、は null を返します。

次の例では、ハートビートレコードからの最後のオクテットを抽出し `ComputerIP` ます。

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

次の例では、最後のオクテットを抽出し、それを *実数* 型 (number) にキャストして、次の IP 値を計算します。

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

次の例で `Trace` は、の定義に対して文字列が検索され `Duration` ます。 一致は、 `real` time 定数 (1 s) にキャストされ、その後、型にキャストされ `Duration` `timespan` ます。

```kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


### <a name="isempty-isnotempty-notempty"></a>*isempty*、 *isnotempty*、 *notempty*

- `isempty``true`引数が空の文字列または null の場合はを返します (「」を参照してください `isnull` )。
- `isnotempty``true`引数が空の文字列または null でない場合はを返します (「」を参照 `isnotnull` )。 エイリアス: `notempty` 。


```kusto
isempty(value)
isnotempty(value)
```

#### <a name="example"></a>例

```kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


### <a name="parseurl"></a>*parseurl*

プロトコル、ホスト、ポートなどの URL をその部分に分割し、その部分を文字列として含む dictionary オブジェクトを返します。

```
parseurl(urlstring)
```

#### <a name="example"></a>例

```kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

出力は次のようになります。

```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```

### <a name="replace"></a>*replace*

正規表現のすべての一致を別の文字列で置き換えます。 

```
replace(regex, rewrite, input_text)
```

- `regex`: によって照合される正規表現。 かっこ内にキャプチャグループを含めることができ \( \) ます。
- `rewrite`: 正規表現を照合することによって行われるすべての一致の置換 regex。 2番目のキャプチャグループについては、\ 0 を使用して、最初のキャプチャグループについては \ 1、それ以降のキャプチャグループでは \ 1 を参照してください。
- `input_text`: 検索する入力文字列。

正規表現のすべての一致を、書き換えの評価と置換した後のテキストを返します。 一致が重複することはありません。

#### <a name="example"></a>例

```kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

出力は次のようになります。

アクティビティ                                        |置換後
------------------------------------------------|----------------------------------------------------------
4663 - オブジェクトへのアクセスが試行されました  |アクティビティ ID 4663: オブジェクトへのアクセスが試行されました。


### <a name="split"></a>*split*

指定した区切り記号に従って特定の文字列を分割し、結果として得られる部分文字列の配列を返します。

```
split(source, delimiter [, requestedIndex])
```

- `source`: 指定された区切り記号に従って分割される文字列。
- `delimiter`: ソース文字列を分割するために使用される区切り記号。
- `requestedIndex`: 0 から始まるインデックス (省略可能)。 指定した場合、返される文字列配列にはその項目だけが格納されます (存在する場合)。


#### <a name="example"></a>例

```kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

### <a name="strcat"></a>*strcat*

文字列引数を連結します (1 から 16 の引数をサポート)。

```
strcat("string1", "string2", "string3")
```

#### <a name="example"></a>例

```kusto
print strcat("hello", " ", "world") // result: "hello world"
```


### <a name="strlen"></a>*strlen*

文字列の長さを返します。

```
strlen("text_to_evaluate")
```

#### <a name="example"></a>例

```kusto
print strlen("hello")   // result: 5
```


### <a name="substring"></a>*substring*

指定したインデックスを開始位置として、特定のソース文字列から部分文字列を抽出します。 必要に応じて、要求された部分文字列の長さを指定できます。

```
substring(source, startingIndex [, length])
```

- `source`: 部分文字列の取得元のソース文字列。
- `startingIndex`: 要求された部分文字列の0から始まる開始文字位置。
- `length`: 返される部分文字列の要求された長さを指定するために使用できる省略可能なパラメーター。

#### <a name="example"></a>例

```kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


### <a name="tolower-toupper"></a>*tolower*、 *toupper*

特定の文字列をすべて小文字または大文字に変換します。

```
tolower("value")
toupper("value")
```

#### <a name="example"></a>例

```kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```

## <a name="date-and-time-operations"></a>日付と時刻の操作

次のセクションでは、Kusto クエリ言語を使用する場合の日付と時刻の値の操作方法の例を示します。

### <a name="date-time-basics"></a>日付と時刻の基本

Kusto クエリ言語には、日付と時刻に関連付けられた2つの主なデータ型 (および) があり `datetime` `timespan` ます。 日付はすべて UTC で表されています。 複数の日付/時刻形式がサポートされていますが、ISO-8601 形式が推奨されます。 

timespan を表現するには、10 進数の後に時間単位を続けます。

|短縮形   | 時間の単位    |
|:---|:---|
|d           | day          |
|h           | hour         |
|m           | minute       |
|s           | second       |
|ms          | ミリ秒  |
|マイクロ秒 | マイクロ秒  |
|tick        | ナノ秒   |

日付/時刻値は、演算子を使用して文字列をキャストすることによって作成でき `todatetime` ます。 たとえば、特定の期間に送信された VM のハートビートを確認するには、演算子を使用して `between` 時間の範囲を指定します。

```kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

もう1つの一般的なシナリオは、日付/時刻値と現在のを比較することです。 たとえば、過去 2 分間のすべてのハートビートを確認するには、`now` 演算子と 2 分を示す timespan を一緒に使用します。

```kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

この関数にはショートカットも使用できます。

```kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

最も短い方法と最も読みやすい方法は、演算子を使用する方法です `ago` 。

```kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

開始時刻と終了時刻を把握するのではなく、開始時刻と継続時間を把握しているとします。 クエリを書き直すことができます。

```kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

### <a name="convert-time-units"></a>時間単位の変換

既定以外の時間単位で日付/時刻値または timespan 値を表すことができます。 たとえば、過去30分間に発生したエラーイベントを確認していて、そのイベントが発生した時間を示す計算列が必要な場合は、次のクエリを使用できます。

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

列には `timeAgo` `00:09:31.5118992` 、hh: mm: fffffff として書式設定されているなどの値が保持されます。 これらの値の書式を `number` 開始時刻から分単位に設定するには、その値を次のように除算し `1m` ます。

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```

### <a name="aggregations-and-bucketing-by-time-intervals"></a>集計と期間でのバケット

もう1つの一般的なシナリオは、特定の時間単位で特定の期間の統計情報を取得する必要があることです。 このシナリオでは、 `bin` 句の一部として演算子を使用でき `summarize` ます。

過去30分間に発生したイベントの数を取得するには、次のクエリを使用します。

```kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

このクエリでは、次のテーブルが生成されます。  

|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

結果のバケットを作成するもう1つの方法は、次のような関数を使用することです `startofday` 。

```kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

出力は次のようになります。

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


### <a name="time-zones"></a>タイム ゾーン

すべての日付/時刻値は UTC で表されるため、多くの場合、これらの値をローカルタイムゾーンに変換すると便利です。 たとえば、次の計算を使用すると、UTC が PST 時間に変換されます。

```kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="aggregations"></a>集計

次のセクションでは、Kusto クエリ言語を使用するときにクエリの結果を集計する方法の例を示します。

### <a name="count"></a>*count*

任意のフィルターが適用された結果セット内の行数をカウントします。 次の例では、過去30分間のテーブル内の行の合計数を返し `Perf` ます。 `count_`列に特定の名前を割り当てない限り、結果はという名前の列に返されます。


```kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

線上の視覚化は、時間の経過に伴う傾向を確認するのに役立つ場合があります。

```kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

この例の出力は、 `Perf` 5 分間隔で記録数の傾向線を示しています。


:::image type="content" source="images/samples/perf-count-line-chart.png" alt-text="パフォーマンスレコード数の傾向線を5分間隔で示す折れ線グラフのスクリーンショット。":::

### <a name="dcount-dcountif"></a>*dcount*、 *dcountif*

`dcount` と `dcountif` を使用して、特定の列の個別の値をカウントします。 次のクエリでは、過去 1 時間にハートビートを送信した個別のコンピューターの数が評価されます。

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

ハートビートを送信した Linux コンピューターだけをカウントするには、`dcountif` を使用します。

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluate-subgroups"></a>サブグループの評価

ご自身のデータのサブグループでカウントまたはその他の集計を実行するには、`by` キーワードを使用します。 たとえば、各国または地域でハートビートを送信した個別の Linux コンピューターの数をカウントするには、次のクエリを使用します。

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|United States    | 19                  |
|Canada           | 3                   |
|アイルランド          | 0                   |
|イギリス   | 0                   |
|オランダ      | 2                   |


データのより小さなサブグループを分析するには、セクションに列名を追加し `by` ます。 たとえば、オペレーティングシステムの種類 () ごとに、国または地域ごとに異なるコンピューターをカウントすることができ `OSType` ます。

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```


### <a name="percentile"></a>パーセンタイル

中央値を見つけるには、`percentile` 関数と、パーセンタイルを指定する値を使用します。

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

また、別のパーセンタイルを指定して、それぞれの集計結果を取得することもできます。

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

結果として、一部のコンピューター Cpu の中央値が類似していることがわかります。 ただし、一部のコンピューターでは中央値が一定の割合で低下しますが、他のコンピューターでは CPU 値が非常に低いことが報告されています。 値の上限と下限は、コンピューターのスパイクが発生していることを意味します。

### <a name="variance"></a>Variance

値の分散を直接評価するには、標準偏差法と分散法を使用します。

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

CPU 使用率の安定性を分析する良い方法は、中央値計算を組み合わせることです `stdev` 。

```kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

### <a name="generate-lists-and-sets"></a>リストとセットの生成

を使用すると、 `makelist` 特定の列の値の順序でデータをピボットできます。 たとえば、コンピューターで発生する最も一般的な順序イベントを調査することができます。 基本的に、各コンピューターの値の順序でデータをピボットでき `EventID` ます。 

```kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

出力は次のようになります。

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` は、データが渡された順序でリストを生成します。 古い順にイベントを並べ替えるには、 `asc` の代わりにをステートメントで使用し `order` `desc` ます。 

個別の値のリストを作成すると便利な場合があります。 この一覧は _セット_ と呼ばれ、次のコマンドを使用して生成でき `makeset` ます。

```kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

出力は次のようになります。

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

と同様 `makelist` に、は順序指定さ `makeset` れたデータでも動作します。 コマンドは、 `makeset` 渡された行の順序に基づいて配列を生成します。

### <a name="expand-lists"></a>リストの展開

またはの逆の演算 `makelist` `makeset` が `mv-expand` です。 コマンドは、 `mv-expand` 値の一覧を展開して行を区切ります。 JSON や配列列など、任意の数の動的列にまたがって拡張できます。 たとえば、 `Heartbeat` 過去1時間にハートビートを送信したコンピューターからデータを送信したソリューションをテーブルで確認できます。

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

出力は次のようになります。

| Computer | ソリューション | 
|--------------|----------------------|
| computer1 | "security"、"updates"、"changeTracking" |
| computer2 | "security"、"updates" |
| computer3 | "antiMalware"、"changeTracking" |
| ... | ... |

を使用すると、 `mv-expand` コンマ区切りのリストではなく、個別の行に各値を表示できます。

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mv-expand Solutions
```

出力は次のようになります。

| Computer | ソリューション | 
|--------------|----------------------|
| computer1 | "security" |
| computer1 | "updates" |
| computer1 | "changeTracking" |
| computer2 | "security" |
| computer2 | "updates" |
| computer3 | "antiMalware" |
| computer3 | "changeTracking" |
| ... | ... |


を使用すると、 `makelist` 項目をグループ化できます。 出力には、ソリューションごとのコンピューターの一覧が表示されます。

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mv-expand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

出力は次のようになります。

|ソリューション | list_Computer |
|--------------|----------------------|
| "security" | ["computer1", "computer2"] |
| "updates" | ["computer1", "computer2"] |
| "changeTracking" | ["computer1", "computer3"] |
| "antiMalware" | ["computer3"] |
| ... | ... |

### <a name="missing-bins"></a>欠落しているビン

の便利なアプリケーションで `mv-expand` は、欠落しているビンの既定値が入力されます。たとえば、ハートビートを調べて、特定のコンピューターの稼働時間を探しているとします。 また、列にあるハートビートのソースも確認し `Category` ます。 通常は、基本的なステートメントを使用し `summarize` ます。

```kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

出力は次のようになります。

| カテゴリ | TimeGenerated | count_ |
|--------------|----------------------|--------|
| 直接エージェント | 2017-06-06T17:00:00Z | 15 |
| 直接エージェント | 2017-06-06T18:00:00Z | 60 |
| 直接エージェント | 2017-06-06T20:00:00Z | 55 |
| 直接エージェント | 2017-06-06T21:00:00Z | 57 |
| 直接エージェント | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

出力では、"2017-06-06T19:00: 00Z" に関連付けられているバケットが不足しています。これは、その時間のハートビートデータが存在しないためです。 `make-series` 関数を使用して、空のバケットに既定値を割り当てます。 カテゴリごとに行が生成されます。 出力には、値用と時間バケットの照合用の2つの配列列が含まれています。

```kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

出力は次のようになります。

| カテゴリ | count_ | TimeGenerated |
|---|---|---|
| 直接エージェント | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

*Count_* 配列の3番目の要素は、想定どおり0です。 _Timegenerated_ 配列には、"2017-06-06T19:00: 00.0000000 z" という照合タイムスタンプがあります。 しかし、この配列形式は読みにくくなります。 `mv-expand` を使用して配列を展開し、`summarize` で生成されたものと同じ形式を出力します。

```kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mv-expand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

出力は次のようになります。

| カテゴリ | TimeGenerated | count_ |
|--------------|----------------------|--------|
| 直接エージェント | 2017-06-06T17:00:00Z | 15 |
| 直接エージェント | 2017-06-06T18:00:00Z | 60 |
| 直接エージェント | 2017-06-06T19:00:00Z | 0 |
| 直接エージェント | 2017-06-06T20:00:00Z | 55 |
| 直接エージェント | 2017-06-06T21:00:00Z | 57 |
| 直接エージェント | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



### <a name="narrow-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>要素のセットに結果を絞り込む: *let*、 *makeset*、 *toscalar*、 *in*

一般的なシナリオでは、一連の条件に基づいて特定のエンティティの名前を選択し、そのエンティティセットに対して別のデータセットをフィルター処理します。 たとえば、更新プログラムが不足していることがわかっているコンピューターを見つけて、これらのコンピューターから呼び出された IP アドレスを特定できます。

次に例を示します。

```kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="joins"></a>結合

結合を使用すると、同じクエリ内の複数のテーブルのデータを分析できます。 結合は、指定された列の値を照合することによって、2つのデータセットの行をマージします。

次に例を示します。

```kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

この例では、最初のデータセットはすべてのサインインイベントをフィルター処理します。 このデータセットは、すべてのサインアウトイベントをフィルター処理する2番目のデータセットと結合されます。 射影される列は、、、 `Computer` `Account` `TargetLogonId` 、および `TimeGenerated` です。 データセットは、共有列 () によって関連付けられてい `TargetLogonId` ます。 出力は、サインインとサインアウトの両方の時間を持つ相関関係ごとに1つのレコードです。

両方のデータセットに同じ名前の列がある場合、右側のデータセットの列にはインデックス番号が付けられます。 この例では、結果には `TargetLogonId` 左側のテーブルの値と右側のテーブルの値が表示され `TargetLogonId1` ます。 この場合、2番目の `TargetLogonId1` 列は演算子を使用して削除されてい `project-away` ます。

> [!NOTE]
> パフォーマンスを向上させるには、演算子を使用して、結合されたデータセットの関連する列のみを保持し `project` ます。


結合されたキーの名前が2つのテーブル間で異なる2つのデータセットを結合するには、次の構文を使用します。

```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

### <a name="lookup-tables"></a>参照テーブル

結合の一般的な用途は、 `datatable` 静的な値のマッピングにを使用することです。 を使用 `datatable` すると、結果の体裁を高めることができます。 たとえば、各イベント ID のイベント名を使用して、セキュリティイベントデータを強化できます。

```kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

出力は次のようになります。

| eventName | count_ |
|:---|:---|
| オブジェクトへのハンドルが閉じられました | 290995 |
| オブジェクトへのハンドルが要求されました | 154157 |
| オブジェクトへのハンドルを複製しようとしました | 144305 |
| オブジェクトにアクセスしようとしました | 123669 |
| 暗号化操作 | 153495 |
| キーファイル操作 | 153496 |

## <a name="json-and-data-structures"></a>JSON とデータ構造

入れ子になったオブジェクトは、配列またはキーと値のペアのマップ内の他のオブジェクトを含むオブジェクトです。 オブジェクトは JSON 文字列として表されます。 このセクションでは、JSON を使用してデータを取得し、入れ子になったオブジェクトを分析する方法について説明します。

### <a name="work-with-json-strings"></a>JSON 文字列を操作する

既知のパスの特定の JSON 要素にアクセスするには、`extractjson` を使用します。 この関数には、次の規則を使用するパス式が必要です。

- _$_ ルートフォルダーを参照するには、を使用します。
- 次の例に示すように、インデックスおよび要素を参照するために、角かっこまたはドット表記を使用します。


インデックスには角かっこを使用し、要素を区切るにはドットを使用します:

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

この例は似ていますが、角かっこのみを使用しています。

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

要素が1つだけの場合は、ドット表記のみを使用できます。

```kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


### <a name="parsejson"></a>*parsejson*

JSON 構造内の複数の要素に動的オブジェクトとしてアクセスするのが最も簡単です。 テキスト データを動的オブジェクトにキャストするには、`parsejson` を使用します。 JSON を動的な型に変換した後は、追加の関数を使用してデータを分析できます。

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```

### <a name="arraylength"></a>*arraylength*

配列内の要素の数を数えるには、`arraylength` を使用します:

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mv-expand"></a>*mv-expand*

`mv-expand`オブジェクトのプロパティを別の行に分割するには、次のように使用します。

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mv-expand hosts_object.hosts[0]
```

:::image type="content" source="images/samples/mvexpand-rows.png" alt-text="スクリーンショットは、host_0 と、location、status、 rate の値を示しています。":::

### <a name="buildschema"></a>*buildschema*

オブジェクトのすべての値に対応するスキーマを取得するには、`buildschema` を使用します:

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

結果は JSON 形式のスキーマになります。

```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```

スキーマでは、オブジェクトフィールドの名前と、それらに対応するデータ型が記述されています。 

入れ子になったオブジェクトには、次の例のように、スキーマが異なる場合があります。

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

## <a name="charts"></a>グラフ

以下のセクションでは、Kusto クエリ言語を使用する場合のグラフの操作方法の例を紹介します。

### <a name="chart-the-results"></a>結果をグラフ化する

最初に、過去1時間以内にオペレーティングシステムごとのコンピューター数を確認します。

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

既定では、結果はテーブルとして表示されます。

:::image type="content" source="images/samples/query-results-table.png" alt-text="クエリ結果を表で示すスクリーンショット。":::

さらに便利なビューを表示するには、[ **グラフ**] を選択し、[ **円** ] オプションを選択して結果を視覚化します。

:::image type="content" source="images/samples/query-results-pie-chart.png" alt-text="クエリ結果を円グラフで示すスクリーンショット。":::

### <a name="timecharts"></a>時間グラフ

プロセッサ時間の平均と50番目および95パーパーセンタイルを1時間のビン単位で表示します。 

次のクエリでは、複数の系列が生成されます。 結果では、線上に表示する系列を選択できます。

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

**折れ線** グラフの表示オプションを選択します。

:::image type="content" source="images/samples/multiple-series-line-chart.png" alt-text="複数系列の折れ線グラフを示すスクリーンショット。":::

#### <a name="reference-line"></a>基準線

参照行は、メトリックが特定のしきい値を超えたかどうかを簡単に識別するのに役立ちます。 グラフに線を追加するには、定数列を追加してデータセットを拡張します。

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

:::image type="content" source="images/samples/multiple-series-threshold-line-chart.png" alt-text="しきい値の参照線を持つ複数系列の折れ線グラフを示すスクリーンショット。":::


### <a name="multiple-dimensions"></a>複数のディメンション

の句に複数の式がある場合 `by` `summarize` 、結果には複数の行が作成されます。 値の組み合わせごとに1つの行が作成されます。

```kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

結果をグラフとして表示する場合、グラフでは句の最初の列が使用され `by` ます。 次の例は、値を使用して作成された積み上げ縦棒グラフを示して `EventID` います。 ディメンションの種類はである必要があり `string` ます。 この例では、 `EventID` 値は次のようにキャストされ `string` ます。

:::image type="content" source="images/samples/select-column-chart-type-eventid.png" alt-text="EventID 列に基づく横棒グラフを示すスクリーンショット。":::

列名のドロップダウン矢印を選択して、列を切り替えることができます。

:::image type="content" source="images/samples/select-column-chart-type-accounttype.png" alt-text="列セレクターが表示されている、AccountType 列に基づく横棒グラフを示すスクリーンショット。":::

## <a name="smart-analytics"></a>スマート分析

このセクションには、Azure Log Analytics のスマート分析機能を使用してユーザーアクティビティを分析する例が含まれています。 これらの例を使用して、Azure アプリケーション Insights によって監視されている独自のアプリケーションを分析したり、他のデータに対する同様の分析のためにこれらのクエリの概念を使用したりすることができます。 

### <a name="cohorts-analytics"></a>コーホート分析

Cohort 分析は、 _コーホート_ と呼ばれる特定のユーザーグループのアクティビティを追跡します。 Cohort 分析は、ユーザーの返品率を測定することで、サービスの魅力を測定しようとします。 ユーザーは、最初にサービスを使用したときにグループ化されます。 コーホートを分析する場合、最初の追跡対象期間でアクティビティが減少することが見込まれています。 各コーホートには、そのメンバーが初めて観察された週ごとにタイトルが付けられています。

次の例では、ユーザーが最初にサービスを使用してから5週間以内に完了したアクティビティの数を分析します。

```kusto
let startDate = startofweek(bin(datetime(2017-01-20T00:00:00Z), 1d));
let week = range Cohort from startDate to datetime(2017-03-01T00:00:00Z) step 7d;
// For each user, we find the first and last timestamp of activity
let FirstAndLastUserActivity = (end:datetime) 
{
    customEvents
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    // Check 30 days back to see first time activity.
    | where timestamp > startDate - 30d
    | where timestamp < end      
    | summarize min=min(timestamp), max=max(timestamp) by user_AuthenticatedId
};
let DistinctUsers = (cohortPeriod:datetime, evaluatePeriod:datetime) {
    toscalar (
    FirstAndLastUserActivity(evaluatePeriod)
    // Find members of the cohort: only users that were observed in this period for the first time.
    | where min >= cohortPeriod and min < cohortPeriod + 7d  
    // Pick only the members that were active during the evaluated period or after.
    | where max > evaluatePeriod - 7d
    | summarize dcount(user_AuthenticatedId)) 
};
week 
| where Cohort == startDate
// Finally, calculate the desired metric for each cohort. In this sample, we calculate distinct users but you can change
// this to any other metric that would measure the engagement of the cohort members.
| extend 
    r0 = DistinctUsers(startDate, startDate+7d),
    r1 = DistinctUsers(startDate, startDate+14d),
    r2 = DistinctUsers(startDate, startDate+21d),
    r3 = DistinctUsers(startDate, startDate+28d),
    r4 = DistinctUsers(startDate, startDate+35d)
| union (week | where Cohort == startDate + 7d 
| extend 
    r0 = DistinctUsers(startDate+7d, startDate+14d),
    r1 = DistinctUsers(startDate+7d, startDate+21d),
    r2 = DistinctUsers(startDate+7d, startDate+28d),
    r3 = DistinctUsers(startDate+7d, startDate+35d) )
| union (week | where Cohort == startDate + 14d 
| extend 
    r0 = DistinctUsers(startDate+14d, startDate+21d),
    r1 = DistinctUsers(startDate+14d, startDate+28d),
    r2 = DistinctUsers(startDate+14d, startDate+35d) )
| union (week | where Cohort == startDate + 21d 
| extend 
    r0 = DistinctUsers(startDate+21d, startDate+28d),
    r1 = DistinctUsers(startDate+21d, startDate+35d) ) 
| union (week | where Cohort == startDate + 28d 
| extend 
    r0 = DistinctUsers (startDate+28d, startDate+35d) )
// Calculate the retention percentage for each cohort by weeks
| project Cohort, r0, r1, r2, r3, r4,
          p0 = r0/r0*100,
          p1 = todouble(r1)/todouble (r0)*100,
          p2 = todouble(r2)/todouble(r0)*100,
          p3 = todouble(r3)/todouble(r0)*100,
          p4 = todouble(r4)/todouble(r0)*100 
| sort by Cohort asc
```

出力は次のようになります。

:::image type="content" source="images/samples/cohorts-table.png" alt-text="アクティビティに基づくコーホートテーブルを示すスクリーンショット。":::

### <a name="rolling-monthly-active-users-and-user-stickiness"></a>変化する月間アクティブ ユーザーとユーザーの持続性

次の例では、 [series_fir](/azure/kusto/query/series-firfunction) 関数を使用した時系列分析を使用します。 `series_fir`スライディングウィンドウ計算には、関数を使用できます。 監視対象のサンプル アプリケーションは、カスタム イベントを通じてユーザーのアクティビティを追跡するオンライン ストアです。 このクエリは、との2種類のユーザーアクティビティを追跡し `AddToCart` `Checkout` ます。 特定の日に少なくとも1回チェックアウトを完了したユーザーとしてアクティブユーザーを定義します。

```kusto
let endtime = endofday(datetime(2017-03-01T00:00:00Z));
let window = 60d;
let starttime = endtime-window;
let interval = 1d;
let user_bins_to_analyze = 28;
// Create an array of filters coefficients for series_fir(). A list of '1' in our case will produce a simple sum.
let moving_sum_filter = toscalar(range x from 1 to user_bins_to_analyze step 1 | extend v=1 | summarize makelist(v)); 
// Level of engagement. Users will be counted as engaged if they completed at least this number of activities.
let min_activity = 1;
customEvents
| where timestamp > starttime  
| where customDimensions["sourceapp"] == "ai-loganalyticsui-prod"
// We want to analyze users who actually checked out in our website.
| where (name == "Checkout") and user_AuthenticatedId <> ""
// Create a series of activities per user.
| make-series UserClicks=count() default=0 on timestamp 
    in range(starttime, endtime-1s, interval) by user_AuthenticatedId
// Create a new column that contains a sliding sum. 
// Passing 'false' as the last parameter to series_fir() prevents normalization of the calculation by the size of the window.
// For each time bin in the *RollingUserClicks* column, the value is the aggregation of the user activities in the 
// 28 days that preceded the bin. For example, if a user was active once on 2016-12-31 and then inactive throughout 
// January, then the value will be 1 between 2016-12-31 -> 2017-01-28 and then 0s. 
| extend RollingUserClicks=series_fir(UserClicks, moving_sum_filter, false)
// Use the zip() operator to pack the timestamp with the user activities per time bin.
| project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
// Transpose the table and create a separate row for each combination of user and time bin (1 day).
| mv-expand RollingUserClicksByDay
| extend Timestamp=todatetime(RollingUserClicksByDay[0])
// Mark the users that qualify according to min_activity.
| extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
// And finally, count the number of users per time bin.
| summarize sum(RollingActiveUsersByDay) by Timestamp
// First 28 days contain partial data, so we filter them out.
| where Timestamp > starttime + 28d
// Render as timechart.
| render timechart
```

出力は次のようになります。

:::image type="content" source="images/samples/rolling-monthly-active-users-chart.png" alt-text="1か月の間にアクティブなユーザーのロールを日単位で示すグラフのスクリーンショット。":::

次の例では、前のクエリを再利用可能な関数に変換します。 この例では、クエリを使用して、ユーザーのロールアウトを計算します。 このクエリのアクティブなユーザーは、特定の日に少なくとも1回チェックアウトを完了したユーザーとして定義されます。

```kusto
let rollingDcount = (sliding_window_size: int, event_name:string)
{
    let endtime = endofday(datetime(2017-03-01T00:00:00Z));
    let window = 90d;
    let starttime = endtime-window;
    let interval = 1d;
    let moving_sum_filter = toscalar(range x from 1 to sliding_window_size step 1 | extend v=1| summarize makelist(v));    
    let min_activity = 1;
    customEvents
    | where timestamp > starttime
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    | where (name == event_name)
    | where user_AuthenticatedId <> ""
    | make-series UserClicks=count() default=0 on timestamp 
        in range(starttime, endtime-1s, interval) by user_AuthenticatedId
    | extend RollingUserClicks=fir(UserClicks, moving_sum_filter, false)
    | project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
    | mv-expand RollingUserClicksByDay
    | extend Timestamp=todatetime(RollingUserClicksByDay[0])
    | extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
    | summarize sum(RollingActiveUsersByDay) by Timestamp
    | where Timestamp > starttime + 28d
};
// Use the moving_sum_filter with bin size of 28 to count MAU.
rollingDcount(28, "Checkout")
| join
(
    // Use the moving_sum_filter with bin size of 1 to count DAU.
    rollingDcount(1, "Checkout")
)
on Timestamp
| project sum_RollingActiveUsersByDay1 *1.0 / sum_RollingActiveUsersByDay, Timestamp
| render timechart
```

出力は次のようになります。

:::image type="content" source="images/samples/user-stickiness-chart.png" alt-text="時間の経過と共にユーザーの持続性を示すグラフのスクリーンショット。":::

### <a name="regression-analysis"></a>回帰分析

この例は、アプリケーションのトレース ログのみに基づいてサービス中断の自動検出機能を作成する方法を示しています。 検出は異常にシークし、アプリケーション内のエラーと警告のトレースの相対的な量が急増します。

トレース ログ データに基づいてサービスの状態を評価するために、2 つの手法が使用されています。

- [make-series](/azure/kusto/query/make-seriesoperator) を使用して、半構造化テキスト トレースログを、正および負のトレース線の比率を表すメトリックに変換します。
- [Series_fit_2lines](/azure/kusto/query/series-fit-2linesfunction)と[series_fit_line](/azure/kusto/query/series-fit-linefunction)を使用して、2行の線形回帰による時系列分析を使用した高度なステップジャンプ検出を実行できます。

```kusto
let startDate = startofday(datetime("2017-02-01"));
let endDate = startofday(datetime("2017-02-07"));
let minRsquare = 0.8;  // Tune the sensitivity of the detection sensor. Values close to 1 indicate very low sensitivity.
// Count all Good (Verbose + Info) and Bad (Error + Fatal + Warning) traces, per day.
traces
| where timestamp > startDate and timestamp < endDate
| summarize 
    Verbose = countif(severityLevel == 0),
    Info = countif(severityLevel == 1), 
    Warning = countif(severityLevel == 2),
    Error = countif(severityLevel == 3),
    Fatal = countif(severityLevel == 4) by bin(timestamp, 1d)
| extend Bad = (Error + Fatal + Warning), Good = (Verbose + Info)
// Determine the ratio of bad traces, from the total.
| extend Ratio = (todouble(Bad) / todouble(Good + Bad))*10000
| project timestamp , Ratio
// Create a time series.
| make-series RatioSeries=any(Ratio) default=0 on timestamp in range(startDate , endDate -1d, 1d) by 'TraceSeverity' 
// Apply a 2-line regression to the time series.
| extend (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(RatioSeries)
// Find out if our 2-line is trending up or down.
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(LineFit2)
// Check whether the line fit reaches the threshold, and if the spike represents an increase (rather than a decrease).
| project PatternMatch = iff(RSquare2 > minRsquare and Slope>0, "Spike detected", "No Match")
```

## <a name="next-steps"></a>次のステップ

- [Kusto クエリ言語のチュートリアルについ](tutorial.md?pivots=azuremonitor)て説明します。


::: zone-end

