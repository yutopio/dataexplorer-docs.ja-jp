---
title: サンプル-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのサンプルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/08/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b2b304d012ea541f6855091874be8ea5483fae63
ms.sourcegitcommit: b6f0f112b6ddf402e97c011a902bd70ba408e897
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2020
ms.locfileid: "94497637"
---
# <a name="samples"></a>サンプル

::: zone pivot="azuredataexplorer"

次に、一般的なクエリのニーズと、Kusto クエリ言語を使用してそれらを満たす方法について説明します。

## <a name="display-a-column-chart"></a>縦棒グラフを表示する

2つ以上の列を射影し、グラフの x 軸と y 軸として使用します。

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* 最初の列は x 軸を形成します。 数値、datetime、または文字列を指定できます。 
* 、、およびを使用して、 `where` `summarize` 表示する `top` データの量を制限します。
* 結果を並べ替えて x 軸の順序を定義します。

:::image type="content" source="images/samples/060.png" alt-text="縦棒グラフのスクリーンショット。Y 軸の範囲は 0 ~ 50 です。10個の色分けされた列は、10個の場所のそれぞれの値を表します。":::

## <a name="get-sessions-from-start-and-stop-events"></a>開始および停止イベントからのセッションの取得

イベントのログがあるとします。 一部のイベントは、拡張されたアクティビティまたはセッションの開始または終了をマークします。 

|名前|City|SessionId|Timestamp|
|---|---|---|---|
|[開始]|London|2817330|2015-12-09T10:12:02.32|
|Game|London|2817330|2015-12-09T10:12:52.45|
|[開始]|Manchester|4267667|2015-12-09T10:14:02.23|
|Stop|London|2817330|2015-12-09T10:23:43.18|
|キャンセル|Manchester|4267667|2015-12-09T10:27:26.29|
|Stop|Manchester|4267667|2015-12-09T10:28:31.72|

すべてのイベントには SessionId があります。 問題は、開始イベントと停止イベントを同じ ID に一致させることです。

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

1. [`let`](./letstatement.md)結合に入る前に、できるだけ減らす下にあるテーブルの射影に名前を付けて使用します。
1. [`Project`](./projectoperator.md)開始時刻と終了時刻の両方が結果に表示されるように、を使用してタイムスタンプの名前を変更します。 
   また、結果に表示される他の列も選択されます。 
1. [`join`](./joinoperator.md)同じアクティビティの開始エントリと停止エントリを一致させ、アクティビティごとに1行を作成するには、を使用します。 
1. 最後に、 `project` はもう一度列を追加して、アクティビティ期間を表示します。


|City|SessionId|StartTime|StopTime|Duration|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Manchester|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

### <a name="get-sessions-without-session-id"></a>セッション ID を使用せずにセッションを取得する

Start イベントと stop イベントには、一致させることのできるセッション ID がないとします。 ただし、セッションが行われたクライアントの IP アドレスはあります。 各クライアントのアドレスでは一度に 1 つのセッションしか行われないと仮定した場合、同じ IP アドレスから各開始イベントと次の停止イベントのマッチングを行うことができます。

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

結合では、同じクライアントの IP アドレスから各開始時刻とすべての停止時刻のマッチングを行います。 
1. 以前の停止時刻と一致するものを削除します。
1. 各セッションのグループを取得するには、[開始時刻と IP] でグループ化します。 
1. `bin`StartTime パラメーターに関数を指定します。 そうでない場合、Kusto は、開始時刻と間違った停止時間を照合する1時間のビンを自動的に使用します。

`arg_min` 各グループの実行時間が最も小さい行を選択し、 `*` パラメーターは他のすべての列を通過します。 引数プレフィックス "min_" を各列名に指定します。 

:::image type="content" source="images/samples/040.png" alt-text="各クライアントの開始時刻の組み合わせについて、開始時刻、クライアント I P、期間、市区町村、および最も早い停止の列を含む結果を一覧表示します。"::: 

コードを追加して、簡単にサイズ設定されたビンで期間をカウントします。この例では、横棒グラフの設定により、をで除算して、 `1s` 時間帯を数値に変換します。 

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/050.png" alt-text="指定された範囲内の期間を持つセッションの数を表す縦棒グラフ。400セッションを超えると10秒後に終了します。100未満は290秒でした。":::

### <a name="real-example"></a>実例

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

開始時刻と終了時刻を含むアクティビティのテーブルがあるとします。 いつでも同時に実行されているアクティビティの数を示すグラフを表示します。

という入力例を次に示し `X` ます。

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

1分のビンのグラフの場合は、実行中のアクティビティごとにカウントされるものを作成します。

ここでは、中間結果を示します。

```kusto
X | extend samples = range(bin(StartTime, 1m), StopTime, 1m)
```

`range` 指定した間隔で値の配列を生成します。

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

次に、これらをサンプル時間でグループ化して、各アクティビティの出現回数をカウントします。

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* [Mv-expand](./mvexpandoperator.md)は動的な型の列を生成するので、todatetime () を使用します。
* Bin () を使用します。数値と日付については、[集計] を指定しない場合は常に既定の間隔で bin 関数が適用されるためです。 


| count_SessionId | サンプル|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

結果は、横棒グラフまたは時間グラフとして表示できます。

## <a name="introduce-null-bins-into-summarize"></a>Null ビンを概要に導入する

列で `summarize` 構成されるグループキーに演算子を適用すると `datetime` 、それらの値が固定幅のビンに "bin" されます。

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

上の例では、 `T` 5 分の各ビンに分類される、の行グループごとに1行のテーブルが生成されます。 では、"null ビン" を追加 `StartTime` し `StopTime` ます。には、に対応する行がないとの間の time bin 値の行が追加され `T` ます。 

これらのビンにテーブルを "埋め込む" ことをお勧めします。これを行う1つの方法を次に示します。

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

1. `union`演算子を使用すると、テーブルに行を追加できます。 これらの行は、式によって生成され `union` ます。
1. 演算子は、 `range` 1 つの行または列を含むテーブルを生成します。
   では、テーブルがでは使用されません `mv-expand` 。
1. `mv-expand`関数に対する演算子は、との `range` 間の5分のビンと同じ数の行を作成し `StartTime` `EndTime` ます。
1. のを `Count` 使用 `0` します。
1. 操作は、 `summarize` 元の引数 (left、outer) からにビンをグループ化し `union` ます。 また、演算子は内部引数からそれにビン分割します (null ビン行)。 このプロセスにより、出力にはビンごとに1行が含まれ、値は0または元のカウントになります。  

## <a name="get-more-out-of-your-data-in-kusto-with-machine-learning"></a>Kusto でデータをさらに活用するには、Machine Learning 

機械学習アルゴリズムを活用し、テレメトリデータから興味深い洞察を得るために、多くの興味深いユースケースがあります。 多くの場合、これらのアルゴリズムでは、入力として非常に構造化されたデータセットが必要です。 通常、未加工のログデータは、必要な構造とサイズに一致しません。 

まず、特定の Bing 推論サービスのエラー率に異常がないか調べてください。 Logs テーブルには65B レコードがあります。 次の単純なクエリは、250,000 エラーをフィルター処理し、異常検出関数 [series_decompose_anomalies](series-decompose-anomaliesfunction.md)を使用するエラー数の時系列データを作成します。 異常は Kusto サービスによって検出され、タイムシリーズグラフの赤い点として強調表示されます。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

サービスでは、疑わしいエラー率を持つ少数のタイムバケットが特定されました。 Kusto を使用して、この時間枠を拡大し、' Message ' 列を集計するクエリを実行します。 上位のエラーを検索してみてください。 

メッセージのスタックトレース全体の関連部分は、ページに合わせてトリミングされます。 

上位8個のエラーが正しく識別されていることを確認できます。 ただし、データの変更を含む書式指定文字列によってエラーメッセージが作成されたため、長い一連のエラーが発生します。 

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| summarize count() by Message 
| top 10 by count_ 
| project count_, Message 
```

|count_|メッセージ
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

ここで演算子が `reduce` 役に立ちます。 演算子は、コード内の同じトレースインストルメンテーションポイントによって発生した63の異なるエラーを特定し、その時間枠での意味のある追加のエラートレースに焦点を当てることができます。

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
|  5112|予期しない推論システム error..System。NullReferenceException: オブジェクト参照がオブジェクトのインスタンスに設定されていません。
|  174|InferenceHostService call failed..System. CommunicationException: パイプへの書き込み中にエラーが発生しました:...
|  63|推論システムエラー..\*要求.: \* オブジェクトに書き込むための書き込み。: "%" というオブジェクトを書き込みます.. \* .
|  10|メソッド ' RunCycleFromInterimData ' の ExecuteAlgorithmMethod に失敗しました...
|  10|推論システムエラー..UserInterimDataManagerException (..........
|  3|InferenceHostService は、tem failed..Sys呼び出します。ServiceModel. \* : オブジェクト (system.servicemodel... \* + \* ) \* \* は \* ...  Syst...

これで、検出された異常に起因する上位のエラーを確認できます。

サンプルシステム間で発生したこれらのエラーの影響を理解するには、次の手順を実行します。 
* ' Logs ' テーブルには、' Component '、' Cluster ' などの追加のディメンションデータが含まれています。
* 新しい "autocluster" プラグインは、単純なクエリを使用してその洞察を導き出すのに役立ちます。 
* 次の例では、上位4つのエラーはそれぞれコンポーネントに固有であることがわかります。 また、上位3つのエラーは DB4 クラスターに固有ですが、4つ目はすべてのクラスターで発生します。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|Count |パーセント (%)|コンポーネント|クラスター|メッセージ
|---|---|---|---|---
|7125|26.64|InferenceHostService|DB4|メソッドの ExecuteAlgorithmMethod....
|7125|26.64|不明なコンポーネント|DB4|InferenceHostService の呼び出しに失敗しました....
|7124|26.64|InferenceAlgorithmExecutor|DB4|予期しない推論システムエラー...
|5112|19.11|InferenceAlgorithmExecutor|*|予期しない推論システムエラー... 

## <a name="map-values-from-one-set-to-another"></a>あるセットから別のセットに値をマップする

一般的なユースケースとして、値の静的なマッピングがあります。これは、結果の体裁を向上させるのに役立ちます。
たとえば、次のテーブルについて考えてみます。 
`DeviceModel` デバイスのモデルを指定します。デバイス名を参照するのに非常に便利な形式ではありません。  


|DeviceModel |Count 
|---|---
|iPhone5、1 |32 
|iPhone3、2 |432 
|iPhone7、2 |55 
|iPhone5、2 |66 

  
次に、より優れた表現を示します。  

|FriendlyName |Count 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

次の2つの方法は、表現を実現する方法を示しています。  

### <a name="mapping-using-dynamic-dictionary"></a>動的ディクショナリを使用したマッピング

この方法では、動的ディクショナリと動的アクセサーを使用したマッピングが示されます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Data set definition
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

### <a name="map-using-static-table"></a>静的テーブルを使用したマップ

この方法では、永続的なテーブルと結合演算子を使用したマッピングが示されます。
 
マッピングテーブルを1回だけ作成します。

```kusto
.create table Devices (DeviceModel: string, FriendlyName: string) 

.ingest inline into table Devices 
    ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
```

現在、デバイスのコンテンツです。

|DeviceModel |FriendlyName 
|---|---
|iPhone5、1 |iPhone 5 
|iPhone3、2 |iPhone 4 
|iPhone7、2 |iPhone 6 
|iPhone5、2 |iPhone5 

テストテーブルソースの作成にも同じトリックを使用します。

```kusto
.create table Source (DeviceModel: string, Count: int)

.ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
```

参加とプロジェクト。

```kusto
Devices  
| join (Source) on DeviceModel  
| project FriendlyName, Count
```

結果:

|FriendlyName |Count 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 


## <a name="create-and-use-query-time-dimension-tables"></a>クエリ時間ディメンションテーブルを作成して使用する

多くの場合、クエリの結果は、データベースに格納されていないアドホックディメンションテーブルと結合する必要があります。 結果が1つのクエリにスコープが設定されたテーブルになる式を定義できます。 次に例を示します。

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
* その他の列

列の各値について最新の2つのレコードを返すクエリ。 `ID` "latest" は、 `timestamp` [最上位の入れ子になった演算子](topnestedoperator.md)で "最高値を持つ" と定義されています。

次に例を示します。

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

以下のメモ番号は、コードサンプル内の数値を示しています。

1. は、 `datatable` デモンストレーション用のテストデータを生成する方法です。 通常、ここでは実際のデータを使用します。
1. この行は基本的に "すべての個別の値を返す" ことを意味 `id` します。
1. 次に、を最大化する上位2つのレコードに対して、次の行が返されます。
     * `timestamp`列
     * 前のレベルの列 (ここでのみ `id` )
     * このレベルで指定された列 (ここでは `timestamp` )
1. この行では、前の `bla` レベルで返された各レコードの列の値が追加されます。 テーブルに関心のある他の列がある場合は、そのような列ごとにこの行を繰り返すことができます。
1. 最後の行では、プロジェクト外の [演算子](projectawayoperator.md) を使用して、によって導入された "extra" 列を削除し `top-nested` ます。

## <a name="extend-a-table-with-some-percent-of-total-calculation"></a>計算される合計の割合を使用してテーブルを拡張する

数値列を含む表形式の式は、と共にユーザーに対して、全体に対する割合としての値と共に使用すると便利です。 たとえば、次のテーブルを生成するクエリがあるとします。

|SomeSeries|Int|
|----------|-------|
|Foo       |    100|
|棒       |    200|

このテーブルを次のように表示する場合:

|SomeSeries|Int|Pct |
|----------|-------|----|
|Foo       |    100|33.3|
|棒       |    200|66.6|

次に、列の合計 (合計) を計算 `SomeInt` してから、この列の各値を合計で除算する必要があります。 任意の結果につい [ては、as 演算子](asoperator.md)を使用します。

```kusto
// The following table literal represents a long calculation
// that ends up with an anonymous tabular value:
datatable (SomeInt:int, SomeSeries:string) [
  100, "Foo",
  200, "Bar",
]
// We now give this calculation a name ("X"):
| as X
// Having this name we can refer to it in the sub-expression
// "X | summarize sum(SomeInt)":
| extend Pct = 100 * bin(todouble(SomeInt) / toscalar(X | summarize sum(SomeInt)), 0.001)
```

## <a name="perform-aggregations-over-a-sliding-window"></a>スライディングウィンドウに対して集計を実行する

次の例では、スライディングウィンドウを使用して列を集計する方法を示します。
次の表を使用します。このテーブルには、タイムスタンプによる果物の価格が含まれています。 7日間のスライディングウィンドウを使用して、1日あたりの各果物の最小、最大、および合計のコストを計算します。 結果セットの各レコードは過去7日間を集計し、結果には分析期間の1日あたりのレコードが含まれます。  

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

スライディングウィンドウの集計クエリ。
クエリの結果には、次のような説明が表示されます。

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

クエリの詳細:

このクエリでは、入力テーブル内の各レコードが実際の外観から7日後に "ストレッチ" (重複) されます。 各レコードは実際には7回表示されます。
その結果、日単位の集計には、過去7日間のすべてのレコードが含まれます。

以下のステップバイステップの説明番号は、コードサンプルの数値を示しています。
1. 各レコードを1日にビン分割します (_start)。 
2. _Bin レコードあたりの範囲の終わりを決定します。 _(開始、終了)_ 範囲外である場合を除きます。ただし、この場合は調整されます。 
3. 各レコードについて、現在のレコードの日から始まる7日間の配列を作成します (タイムスタンプ)。 
4. mv-配列を展開し、各レコードを7つのレコードに複製します。 
5. 日ごとに集計関数を実行します。 #4 のため、これは _過去_ 7 日間をまとめたものです。 
6. 最初の7日間のデータは完全ではありません。 最初の7日間は、7d の期間がありません。 最初の7日間は、最終結果から除外されます。 この例では、2018-10-01 の集計にのみ参加します。

## <a name="find-preceding-event"></a>前のイベントを検索
次の例では、2つのデータセット間の前のイベントを検索する方法を示します。  

*目的:* : a と B の2つのデータセットがあります。B の各レコードについて、の前のイベントを検索します (つまり、"古い" が B よりも前の arg_max レコード)。 次のサンプルデータセットに予想される出力を次に示します。 

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

|Timestamp|id|EventB|
|---|---|---|
|2019-01-01 00:00: 00.0000000|x|Ax1|
|2019-01-01 00:00: 00.0000000|z|Az1|
|2019-01-01 00:00: 01.0000000|x|Ax2|
|2019-01-01 00:00: 02.0000000|○|Ay1|
|2019-01-01 00:00: 05.0000000|○|Ay2|

</br>

|Timestamp|id|EventA|
|---|---|---|
|2019-01-01 00:00: 03.0000000|x|B|
|2019-01-01 00:00: 04.0000000|x|B|
|2019-01-01 00:00: 04.0000000|○|B|
|2019-01-01 00:02: 00.0000000|z|B|

予想される出力: 

|id|Timestamp|EventB|A_Timestamp|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00: 03.0000000|B|2019-01-01 00:00: 01.0000000|Ax2|
|x|2019-01-01 00:00: 04.0000000|B|2019-01-01 00:00: 01.0000000|Ax2|
|○|2019-01-01 00:00: 04.0000000|B|2019-01-01 00:00: 02.0000000|Ay1|
|z|2019-01-01 00:02: 00.0000000|B|2019-01-01 00:00: 00.0000000|Az1|

この問題には、2つの異なる方法が提案されています。 お客様にとって最適なものを見つけるには、特定のデータセットで両方をテストする必要があります。

> [!NOTE] 
> 各方法は、データセットごとに異なる方法で実行される場合があります。

### <a name="suggestion-1"></a>提案 #1

この提案では、ID とタイムスタンプで両方のデータセットをシリアル化した後、すべての B イベントをその前のすべてのイベントとグループ化し、 `arg_max` グループ内のすべてのを取得します。

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

### <a name="suggestion-2"></a>提案 #2

この提案には、B と比較した場合に、最大のバック期間 (のレコードのうち、レコードの "古い" レコードがどれくらい古いか) が必要です。次に、メソッドは、ID とこの2つのデータセットを結合します。 結合により、可能なすべての候補が生成されます。これは、B よりも前のすべてのレコードと、元の場所にあるレコードであり、B に最も近いレコードは、arg_min (タイムスタンプ B –タイムスタンプ A) によってフィルター処理されます。 ルックバック期間が短くなるほど、クエリの結果が向上します。

次の例では、参照元の期間が1m に設定されていて、ID が ' z ' のレコードに対応する ' A ' イベントがありません。これは、' A ' が 2 m を超えているためです。

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

|Id|B_Timestamp|A_Timestamp|EventB|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00: 03.0000000|2019-01-01 00:00: 01.0000000|B|Ax2|
|x|2019-01-01 00:00: 04.0000000|2019-01-01 00:00: 01.0000000|B|Ax2|
|○|2019-01-01 00:00: 04.0000000|2019-01-01 00:00: 02.0000000|B|Ay1|
|z|2019-01-01 00:02: 00.0000000||B||

::: zone-end

::: zone pivot="azuremonitor"

## <a name="string-operations"></a>文字列操作
次のセクションでは、Kusto クエリ言語で文字列を使用する例について説明します。

### <a name="strings-and-escaping-them"></a>文字列とそのエスケープ
文字列の値は、一重引用符または二重引用符のいずれかで囲まれています。 バックスラッシュ (\\) はエスケープ文字で、その次の文字をエスケープします。たとえば、\t はタブを、\n は改行を、そして \" は引用符自体を表します。

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

"\\" がエスケープ文字として機能しないようにするには、文字列に "\@" をプレフィックスとして追加します。

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


### <a name="string-comparisons"></a>文字列の比較

演算子       |説明                         |大文字と小文字の区別|例 (`true` になる)
---------------|------------------------------------|--------------|-----------------------
`==`           |等しい                              |はい           |`"aBc" == "aBc"`
`!=`           |等しくない                          |はい           |`"abc" != "ABC"`
`=~`           |等しい                              |いいえ            |`"abc" =~ "ABC"`
`!~`           |等しくない                          |いいえ            |`"aBc" !~ "xyz"`
`has`          |右辺が左辺の完全な用語として含まれる |いいえ|`"North America" has "america"`
`!has`         |右辺が左辺の完全な用語として含まれない       |いいえ            |`"North America" !has "amer"` 
`has_cs`       |右辺が左辺の完全な用語として含まれる |はい|`"North America" has_cs "America"`
`!has_cs`      |右辺が左辺の完全な用語として含まれない       |はい            |`"North America" !has_cs "amer"` 
`hasprefix`    |右辺が左辺の用語のプレフィックスとして含まれる         |いいえ            |`"North America" hasprefix "ame"`
`!hasprefix`   |右辺が左辺の用語のプレフィックスとして含まれない     |いいえ            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |右辺が左辺の用語のプレフィックスとして含まれる         |はい            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |右辺が左辺の用語のプレフィックスとして含まれない     |はい            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |右辺が左辺の用語のサフィックスとして含まれる         |いいえ            |`"North America" hassuffix "ica"`
`!hassuffix`   |右辺が左辺の用語のサフィックスに含まれない     |いいえ            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |右辺が左辺の用語のサフィックスとして含まれる         |はい            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |右辺が左辺の用語のサフィックスに含まれない     |はい            |`"North America" !hassuffix_cs "icA"`
`contains`     |右辺が左辺のサブシーケンスとして出現する  |いいえ            |`"FabriKam" contains "BRik"`
`!contains`    |右辺が左辺のサブシーケンスとして出現しない           |いいえ            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |右辺が左辺のサブシーケンスとして出現する  |はい           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |右辺が左辺のサブシーケンスとして出現しない           |はい           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |右辺が左辺の先頭のサブシーケンスである|いいえ            |`"Fabrikam" startswith "fab"`
`!startswith`  |右辺が左辺の先頭のサブシーケンスでない|いいえ        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |右辺が左辺の先頭のサブシーケンスである|はい            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |右辺が左辺の先頭のサブシーケンスでない|はい        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |右辺が左辺の末尾のサブシーケンスである|いいえ             |`"Fabrikam" endswith "Kam"`
`!endswith`    |右辺が左辺の末尾のサブシーケンスでない|いいえ         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |右辺が左辺の末尾のサブシーケンスである|はい             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |右辺が左辺の末尾のサブシーケンスでない|はい         |`"Fabrikam" !endswith "brik"`
`matches regex`|左辺には右辺の一致が含まれている        |はい           |`"Fabrikam" matches regex "b.*k"`
`in`           |要素のいずれかに等しい       |はい           |`"abc" in ("123", "345", "abc")`
`!in`          |要素のいずれとも等しくない   |はい           |`"bca" !in ("123", "345", "abc")`


### <a name="countof"></a>countof

文字列内の部分文字列の出現回数をカウントします。 プレーン文字列と照合できます。または正規表現を使用できます。 プレーン文字列の一致は重複する可能性があります。正規表現の一致は重複しません。

```
countof(text, search [, kind])
```

- `text` - 入力文字列 
- `search` - text 内で一致するプレーン文字列または正規表現。
- `kind` - _normal_ | _regex_ (既定: normal)。

コンテナー内で検索文字列が一致する回数を返します。 プレーン文字列の一致は重複する可能性があります。正規表現の一致は重複しません。

#### <a name="plain-string-matches"></a>プレーン文字列の一致

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>正規表現の一致

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


### <a name="extract"></a>extract

指定された文字列から正規表現との一致を取得します。 必要に応じて、抽出された部分文字列を、指定された型に変換することもできます。

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

- `regex` - 正規表現。
- `captureGroup` - 抽出するキャプチャ グループを示す正の整数の定数。 0 は一致全体、1 は正規表現の最初のかっこで囲まれた部分と一致した値、2 以上は後続のかっこを示します。
- `text` - 検索する文字列。
- `typeLiteral` - オプションの type リテラル (例: typeof(long))。 指定した場合、抽出された部分文字列はこの型に変換されます。

指定されたキャプチャグループ captureGroup に一致した部分文字列を返します。これは、必要に応じて Typel Al に変換されます。
一致がないか、型変換が失敗した場合は、null 値を返します。


次の例では、ハートビート レコードから *ComputerIP* の最終オクテットを抽出します。
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

次の例では、最終オクテットを抽出して *real* 型 (数値) にキャストし、次の IP 値を計算します
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

次の例では、文字列 *Trace* で "Duration" の定義を検索します。 一致は *real* にキャストされた後、" *Duration を timespan 型にキャストする* " 時間定数 (1 s) で乗算されます。
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


### <a name="isempty-isnotempty-notempty"></a>isempty、isnotempty、notempty

- *isempty* は、引数が空の文字列または null 値の場合に true を返します ( *isnull* も参照)。
- *isnotempty* は、引数が空の文字列または null 値でない場合に true を返します ( *isnotnull* も参照)。 別名: *notempty* 。


```Kusto
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>例

```Kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


### <a name="parseurl"></a>parseurl

URL をパーツ (プロトコル、ホスト、ポートなど) に分割し、そのパーツが文字列として含まれるディクショナリ オブジェクトを返します。

```
parseurl(urlstring)
```

#### <a name="examples"></a>例

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

結果は次のとおりです。
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


### <a name="replace"></a>replace

正規表現のすべての一致を別の文字列で置き換えます。 

```
replace(regex, rewrite, input_text)
```

- `regex` - 照合する正規表現。 複数のキャプチャ グループをかっこ内に含めることができます。
- `rewrite` - 照合する正規表現と一致したものすべてが置き換えられた後の正規表現。 完全一致を参照する場合は \0、最初のキャプチャ グループの場合は \1、後続のキャプチャ グループの場合は \2 などを使用します。
- `input_text` - 検索場所を示す入力文字列。

正規表現のすべての一致を、書き換えの評価と置換した後のテキストを返します。 一致が重複することはありません。


```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

結果は次のとおりです。

アクティビティ                                        |replaced
------------------------------------------------|----------------------------------------------------------
4663 - オブジェクトへのアクセスが試行されました  |アクティビティ ID 4663: オブジェクトへのアクセスが試行されました。


### <a name="split"></a>split

指定された文字列を、指定された区切り記号に従って分割し、結果の部分文字列の配列を返します。

```
split(source, delimiter [, requestedIndex])
```

- `source` - 指定された区切り記号に従って分割する文字列。
- `delimiter` - ソース文字列の分割に使用される区切り記号。
- `requestedIndex` - 0 から始まるインデックス (省略可能)。 指定した場合、返される文字列配列にはその項目のみが含まれます (存在する場合)。


#### <a name="examples"></a>例

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

### <a name="strcat"></a>strcat

文字列引数を連結します (1 から 16 の引数をサポート)。

```
strcat("string1", "string2", "string3")
```

#### <a name="examples"></a>例
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


### <a name="strlen"></a>strlen

文字列の長さを返します。

```
strlen("text_to_evaluate")
```

#### <a name="examples"></a>例
```Kusto
print strlen("hello")   // result: 5
```


### <a name="substring"></a>substring

指定されたソース文字列から、指定されたインデックス位置以降にある部分文字列を抽出します。 必要に応じて、要求する部分文字列の長さを指定できます。

```
substring(source, startingIndex [, length])
```

- `source` - 部分文字列の取得元となるソース文字列。
- `startingIndex` - 要求する部分文字列の、0 から始まる開始文字位置。
- `length` - 要求する部分文字列に対して返される文字数の指定に使用できる省略可能なパラメーター。

#### <a name="examples"></a>例
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


### <a name="tolower-toupper"></a>tolower、toupper

指定された文字列を、すべて小文字またはすべて大文字に変換します。

```
tolower("value")
toupper("value")
```

#### <a name="examples"></a>例
```Kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```

## <a name="date-and-time-operations"></a>日付と時刻の操作
次のセクションでは、Kusto クエリ言語で日付と時刻の値を使用する例について説明します。

### <a name="date-time-basics"></a>日時の基本
Kusto クエリ言語では、主要な 2 つのデータ型 (datetime と timespan) が日付と時刻に関連付けられています。 日付はすべて UTC で表されています。 複数の datetime 形式がサポートされていますが、ISO8601 形式をお勧めします。 

timespan を表現するには、10 進数の後に時間単位を続けます。

|短縮形   | 時間単位    |
|:---|:---|
|d           | day          |
|h           | hour         |
|m           | minute       |
|s           | second       |
|ms          | ミリ秒  |
|マイクロ秒 | マイクロ秒  |
|tick        | ナノ秒   |

datetime を作成するには、`todatetime` 演算子を使用して文字列をキャストします。 たとえば、特定の期間に送信される VM ハートビートを確認するには、`between` 演算子を使用して時間範囲を指定します。

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

datetime を現在と比較するシナリオも一般的です。 たとえば、過去 2 分間のすべてのハートビートを確認するには、`now` 演算子と 2 分を示す timespan を一緒に使用します。

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

この関数にはショートカットも使用できます。
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

ただし、最も短く読みやすいのは、`ago` 演算子を使用する方法です。
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

たとえば、開始時刻と終了時刻ではなく、開始時刻と期間がわかっているとします。 クエリは次のように書き換えることができます。

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

### <a name="converting-time-units"></a>時間単位の変換
datetime または timespan を既定以外の時間単位で表したい場合があります。 たとえば、過去 30 分間のエラー イベントを確認しているときに、イベントがどのくらい前に発生したかを示す計算列が必要だとします。

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

`timeAgo` 列には "00:09:31.5118992" などの値が含まれていることがわかります。これは書式が hh:mm:ss.fffffff であることを意味します。 これらの値を、開始時刻からの時間 (分) の `numver` に設定する場合は、その値を "1 分" で除算します。

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


### <a name="aggregations-and-bucketing-by-time-intervals"></a>集計と期間でのバケット
さらに、特定の時間グレインで、特定の期間にわたる統計値を取得しなければならないシナリオもよく見られます。 このシナリオでは、`bin` 演算子を、summarize 句の一部として使用できます。

次のクエリを使用すると、過去 30 分間に発生したイベント数を 5 分ごとに取得できます。

```Kusto
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

`startofday` などの関数を使用して、結果のバケットを作成することもできます。

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

このクエリでは、次の結果が生成されます。

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


### <a name="time-zones"></a>タイム ゾーン
すべての datetime 値が UTC で表されるため、多くの場合、これらの値をローカル タイムゾーンに変換すると便利です。 たとえば、次の計算を使用すると、UTC が PST 時間に変換されます。

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="aggregations"></a>集計
次のセクションでは、Kusto クエリ言語でクエリの結果を集計する例を紹介します。

### <a name="count"></a>count
任意のフィルターが適用された結果セット内の行数をカウントします。 次の例では、過去 30 分間にわたる _Perf_ テーブル内の行数の合計が返されます。 結果は、 *count_* という名前の列で返されます (特定の名前を列に割り当てた場合を除く)。


```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

時間グラフの視覚化は、時間の経過に沿った傾向を確認するうえで役立ちます。

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

この例の出力には、perf レコード数のトレンドラインが 5 分間隔で示されています。

![カウントの傾向](images/samples/count-trend.png)


### <a name="dcount-dcountif"></a>dcount、dcountif
`dcount` と `dcountif` を使用して、特定の列の個別の値をカウントします。 次のクエリでは、過去 1 時間にハートビートを送信した個別のコンピューターの数が評価されます。

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

ハートビートを送信した Linux コンピューターだけをカウントするには、`dcountif` を使用します。

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>サブグループの評価
ご自身のデータのサブグループでカウントまたはその他の集計を実行するには、`by` キーワードを使用します。 たとえば、ハートビートを送信した個別の Linux コンピューターの数を国や地域ごとにカウントするには、次を使用します。

```Kusto
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


ご自身のデータのさらに小さなサブグループを分析するには、追加の列名を `by` セクションに追加します。 たとえば、OSType あたりの個別のコンピューターの数を国や地域ごとにカウントできます。

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```


### <a name="percentile"></a>パーセンタイル
中央値を見つけるには、`percentile` 関数と、パーセンタイルを指定する値を使用します。

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

さまざまなパーセンタイルを指定して、それぞれの集計結果を取得することもできます。

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

これは、複数のコンピューター CPU の中央値が似かよっていても、実際は CPU が中央値付近で安定しているコンピューターもあれば、極端に低い CPU または高い CPU 値が報告される、つまりスパイクが発生しているコンピューターもあります。

### <a name="variance"></a>Variance
値の分散を直接評価するには、標準偏差法と分散法を使用します。

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

CPU 使用率の安定性を分析する場合は、stdev と中央値の計算を組み合わせることをお勧めします。

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

### <a name="generating-lists-and-sets"></a>リストとセットの生成
`makelist` を使用して、特定の列にある値の順序でデータをピボットできます。 たとえば、マシンで行われる最も一般的な注文イベントを調査できます。 基本的に言って、各マシンの EventID の順番でデータをピボットできます。 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` は、データが渡された順序でリストを生成します。 イベントを一番前のものから最新のものに並べ替えるには、order ステートメントで `desc` の代わりに `asc` を使用します。 

また、個別の値だけの一覧を作成するのも便利です。 これは _セット_ と呼ばれ、次のように `makeset` で生成できます。

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` と同様に、`makeset` も順序付けされたデータを処理し、渡される行の順序に基づいて配列を生成します。

### <a name="expanding-lists"></a>リストの展開
`makelist` または `makeset` の逆の操作は `mvexpand` です。これは、値のリストを別々の行に展開します。 JSON と配列の両方で、任意の数の動的な列に展開できます。 たとえば、過去 1 時間にハートビートを送信したコンピューターからのデータを送信するソリューションの *Heartbeat* ソリューション テーブルを確認できます。

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Computer | ソリューション | 
|--------------|----------------------|
| computer1 | "security"、"updates"、"changeTracking" |
| computer2 | "security"、"updates" |
| computer3 | "antiMalware"、"changeTracking" |
| ... | ... |

`mvexpand` を使用すると、コンマ区切りリストではなく、次のようにそれぞれの値が個別の行に表示されます。

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

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


次に、もう一度 `makelist` を使用して、項目をまとめてグループ化すると、今度はソリューションごとにコンピューターのリストを表示できます。

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|ソリューション | list_Computer |
|--------------|----------------------|
| "security" | ["computer1", "computer2"] |
| "updates" | ["computer1", "computer2"] |
| "changeTracking" | ["computer1", "computer3"] |
| "antiMalware" | ["computer3"] |
| ... | ... |

### <a name="handling-missing-bins"></a>不足しているビンの処理
`mvexpand` の便利な適用方法の 1 つは、不足しているビンの既定値を埋める必要がある場合です。たとえば、ハートビートを調べて、特定のマシンのアップタイムを確認しているとします。 また、 _Category_ 列に示されているハートビートのソースを確認する必要もあるとします。 通常は、次のように単純な summarize ステートメントを使用することでしょう。

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| カテゴリ | TimeGenerated | count_ |
|--------------|----------------------|--------|
| 直接エージェント | 2017-06-06T17:00:00Z | 15 |
| 直接エージェント | 2017-06-06T18:00:00Z | 60 |
| 直接エージェント | 2017-06-06T20:00:00Z | 55 |
| 直接エージェント | 2017-06-06T21:00:00Z | 57 |
| 直接エージェント | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

これらの結果には、"2017-06-06T19:00:00Z" に関連付けられたバケットはありません。これは、その時間のハートビート データがないためです。 `make-series` 関数を使用して、空のバケットに既定値を割り当てます。 これにより、カテゴリごとに 1 行が生成され、各行には追加の配列の列が 2 つ設けられます。1 つは値、1 つは合致する時間バケットです。

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| カテゴリ | count_ | TimeGenerated |
|---|---|---|
| 直接エージェント | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

*count_* 配列の 3 番目の要素は予想どおり 0 であり、一致するタイムスタンプ "2017-06-06T19：00：00.0000000Z" が _TimeGenerated_  配列にあります。 しかし、この配列形式は読み取りが困難です。 `mvexpand` を使用して配列を展開し、`summarize` で生成されたものと同じ形式を出力します。

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| カテゴリ | TimeGenerated | count_ |
|--------------|----------------------|--------|
| 直接エージェント | 2017-06-06T17:00:00Z | 15 |
| 直接エージェント | 2017-06-06T18:00:00Z | 60 |
| 直接エージェント | 2017-06-06T19:00:00Z | 0 |
| 直接エージェント | 2017-06-06T20:00:00Z | 55 |
| 直接エージェント | 2017-06-06T21:00:00Z | 57 |
| 直接エージェント | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



### <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>結果を要素セットに絞り込む: `let`、`makeset`、`toscalar`、`in`
一般的なシナリオでは、基準のセットに基づいて特定のエンティティの名前を選択し、異なるデータ セットをフィルターして、そのエンティティのセットに絞り込みます。 たとえば、不足している更新プログラムがあることが認識されているコンピューターを見つけ、これらのコンピューターが呼び出した IP を特定することができます。


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="joins"></a>結合
結合を使用すると、同じクエリで、複数のテーブルのデータを分析できます。 これにより、指定された列の値が照合され、2 つのデータ セットの行がマージされます。


```Kusto
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

この例では、最初のデータセットでは、すべてのサインイン イベントがフィルター選択されます。 これは、すべてのサインアウト イベントをフィルター選択する、2 番目のデータセットと結合されます。 予想列は _Computer_ 、 _Account_ 、 _TargetLogonId_ 、および _TimeGenerated_ です。 データセットは、共有列 _TargetLogonId_ で関連付けられています。 相関関係ごとに 1 つのレコードが出力されます。これにはサインイン時間とサインアウト時間の両方が含まれます。

両方のデータセットに同じ名前の列がある場合、右側のデータセットの列にインデックス番号が追加されます。したがって、この例では、結果の _TargetLogonId_ には左側のテーブルの値が、 _TargetLogonId1_ には右側のテーブルの値が示されています。 この場合、2 番目の _TargetLogonId1_ 列は、`project-away` 演算子を使用して削除されました。

> [!NOTE]
> パフォーマンスを向上させるには、`project` 演算子を使用して、結合されたデータセットの関連する列のみを保持します。


次の構文を使用して、2 つのデータセットを結合します。結合されたキーの名前は 2 つのテーブルの間で異なります。
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

### <a name="lookup-tables"></a>参照テーブル
結合の一般的な使用法では、結果をよりわかりやすいものに変換するうえで役立つ、`datatable` を使用した値の静的マッピングが使用されています。 たとえば、セキュリティ イベント データを、各イベント ID に対応するイベント名を使用してわかりやすくするには、次を使用します。

```Kusto
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

| eventName | count_ |
|:---|:---|
| オブジェクトへのハンドルが閉じられました | 290995 |
| オブジェクトへのハンドルが要求されました | 154157 |
| オブジェクトへのハンドルを複製しようとしました | 144305 |
| オブジェクトにアクセスしようとしました | 123669 |
| 暗号化操作 | 153495 |
| キーファイル操作 | 153496 |

## <a name="json-and-data-structures"></a>JSON とデータ構造

入れ子になったオブジェクトは、配列やキーと値のペアのマップ内に他のオブジェクトを含むオブジェクトです。 これらのオブジェクトは JSON 文字列として表されます。 ここでは、JSON を使用してデータを取得し、入れ子になったオブジェクトを分析する方法について説明します。

### <a name="working-with-json-strings"></a>JSON 文字列の使用
既知のパスの特定の JSON 要素にアクセスするには、`extractjson` を使用します。 この関数では、次の規則を使用するパス式が必要です。

- ルート フォルダーを参照するための _$_
- 次の例に示すように、インデックスおよび要素を参照するために、角かっこまたはドット表記を使用します。


インデックスには角かっこを使用し、要素を区切るにはドットを使用します:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

これは角かっこ表記のみを使用していますが、同じ結果になります:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

要素が 1 つしかない場合は、ドット表記のみを使用できます:

```Kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


### <a name="parsejson"></a>parsejson
json 構造内の複数の要素にアクセスするには、動的オブジェクトとして構造にアクセスするのが簡単です。 テキスト データを動的オブジェクトにキャストするには、`parsejson` を使用します。 いったん動的な型に変換すれば、追加の関数を使用してデータを分析できるようになります。

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
配列内の要素の数を数えるには、`arraylength` を使用します:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
オブジェクトのプロパティを個別の行に分割するには、`mvexpand` を使用します。

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![スクリーンショットは、host_0 と、location、status、 rate の値を示しています。](images/samples/mvexpand.png)

### <a name="buildschema"></a>buildschema
オブジェクトのすべての値に対応するスキーマを取得するには、`buildschema` を使用します:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

出力は JSON 形式のスキーマです:
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
この出力には、オブジェクト フィールドの名前と、それに対応するデータ型が記述されています。 

入れ子になったオブジェクトは、次の例のように、さまざまなスキーマがある場合があります:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

## <a name="charts"></a>グラフ
次のセクションでは、Kusto クエリ言語でグラフを使用する例について説明します。

### <a name="chart-the-results"></a>結果をグラフ化する
まず、過去 1 時間のオペレーティング システムあたりのコンピューター数を確認します。

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

既定では、結果はテーブルの形で表示されます。

![テーブル](images/samples/table-display.png)

より見やすくするために、 **[グラフ]** 、 **[円]** オプションの順に選択し、結果を視覚化します。

![円グラフ](images/samples/charts-and-diagrams-pie.png)


### <a name="timecharts"></a>時間グラフ
プロセッサ時間の平均、50 パーセンタイル、95 パーセンタイルを 1 時間のビンに表示します。 クエリによって複数の系列が生成されるので、土の系列を時間グラフに表示するかを選択できます。

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

**折れ線** グラフの表示オプションを選択します。

![折れ線グラフ](images/samples/charts-and-diagrams-multiple-series.png)

#### <a name="reference-line"></a>基準線

基準線を使用すると、メトリックが特定のしきい値を超えているかどうかを簡単に識別できます。 グラフに線を追加するには、定数列を追加してデータセットを拡張します。

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![基準線](images/samples/charts-and-diagrams-multiple-series-threshold.png)

### <a name="multiple-dimensions"></a>複数のディメンション
`summarize` の`by` 句にある複数の式により、結果に複数の行が作成されます。値の組み合わせごとに 1 行ずつです。

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

結果をグラフとして表示するときには、`by` 句の最初の列が使用されます。 次の例は、 _EventID_ を使用した積み上げ縦棒グラフを示しています。 ディメンションは `string` 型である必要があるため、この例では _EventID_ が文字列にキャストされます。 

![棒グラフ EventID](images/samples/charts-and-diagrams-multiple-dimension-1.png)

ドロップダウンで列名を選択すると、切り替えができます。 

![棒グラフ AccountType](images/samples/charts-and-diagrams-multiple-dimension-2.png)

## <a name="smart-analytics"></a>スマート分析
このセクションには、Log Analytics でスマート分析機能を使用してユーザーアクティビティの分析を実行する例が含まれています。 これらの例を利用して、Application Insights によって監視されているお客様のアプリケーションを分析することができます。また、これらのクエリの概念を使用して、他のデータを同様に分析することもできます。 

### <a name="cohorts-analytics"></a>コーホート分析

コーホート分析では、特定のユーザー グループ (コーホートと呼ばれます) のアクティビティを追跡します。 ユーザーのリピート率を測定することで、サービスの魅力度が測られます。 ユーザーは、そのサービスを初めて使用した時間ごとにグループ化されます。 コーホートを分析する場合、最初の追跡対象期間でアクティビティが減少することが見込まれています。 各コーホートには、そのメンバーが初めて観察された週ごとにタイトルが付けられています。

次の例では、ユーザーがそのサービスを初めて使用した後の 5 週間にわたって実行したアクティビティの数を分析しています。

```Kusto
let startDate = startofweek(bin(datetime(2017-01-20T00:00:00Z), 1d));
let week = range Cohort from startDate to datetime(2017-03-01T00:00:00Z) step 7d;
// For each user we find the first and last timestamp of activity
let FirstAndLastUserActivity = (end:datetime) 
{
    customEvents
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    // Check 30 days back to see first time activity
    | where timestamp > startDate - 30d
    | where timestamp < end      
    | summarize min=min(timestamp), max=max(timestamp) by user_AuthenticatedId
};
let DistinctUsers = (cohortPeriod:datetime, evaluatePeriod:datetime) {
    toscalar (
    FirstAndLastUserActivity(evaluatePeriod)
    // Find members of the cohort: only users that were observed in this period for the first time
    | where min >= cohortPeriod and min < cohortPeriod + 7d  
    // Pick only the members that were active during the evaluated period or after
    | where max > evaluatePeriod - 7d
    | summarize dcount(user_AuthenticatedId)) 
};
week 
| where Cohort == startDate
// Finally, calculate the desired metric for each cohort. In this sample we calculate distinct users but you can change
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
この例では、次のように出力されます。

:::image type="content" source="images/samples/cohorts.png" alt-text="コーホート分析の出力":::

### <a name="rolling-monthly-active-users-and-user-stickiness"></a>変化する月間アクティブ ユーザーとユーザーの持続性
次の例では、スライディング ウィンドウ計算を実行できる [series_fir](/azure/kusto/query/series-firfunction) 関数を使用した時系列分析を使用しています。 監視対象のサンプル アプリケーションは、カスタム イベントを通じてユーザーのアクティビティを追跡するオンライン ストアです。 このクエリでは _AddToCart_ と _Checkout_ という 2 種類のユーザー アクティビティを追跡し、 _アクティブ ユーザー_ を特定の日に少なくとも 1 回の精算を実行したユーザーと定義しています。



```Kusto
let endtime = endofday(datetime(2017-03-01T00:00:00Z));
let window = 60d;
let starttime = endtime-window;
let interval = 1d;
let user_bins_to_analyze = 28;
// Create an array of filters coefficients for series_fir(). A list of '1' in our case will produce a simple sum.
let moving_sum_filter = toscalar(range x from 1 to user_bins_to_analyze step 1 | extend v=1 | summarize makelist(v)); 
// Level of engagement. Users will be counted as engaged if they performed at least this number of activities.
let min_activity = 1;
customEvents
| where timestamp > starttime  
| where customDimensions["sourceapp"] == "ai-loganalyticsui-prod"
// We want to analyze users who actually checked-out in our web site
| where (name == "Checkout") and user_AuthenticatedId <> ""
// Create a series of activities per user
| make-series UserClicks=count() default=0 on timestamp 
    in range(starttime, endtime-1s, interval) by user_AuthenticatedId
// Create a new column containing a sliding sum. 
// Passing 'false' as the last parameter to series_fir() prevents normalization of the calculation by the size of the window.
// For each time bin in the *RollingUserClicks* column, the value is the aggregation of the user activities in the 
// 28 days that preceded the bin. For example, if a user was active once on 2016-12-31 and then inactive throughout 
// January, then the value will be 1 between 2016-12-31 -> 2017-01-28 and then 0s. 
| extend RollingUserClicks=series_fir(UserClicks, moving_sum_filter, false)
// Use the zip() operator to pack the timestamp with the user activities per time bin
| project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
// Transpose the table and create a separate row for each combination of user and time bin (1 day)
| mvexpand RollingUserClicksByDay
| extend Timestamp=todatetime(RollingUserClicksByDay[0])
// Mark the users that qualify according to min_activity
| extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
// And finally, count the number of users per time bin.
| summarize sum(RollingActiveUsersByDay) by Timestamp
// First 28 days contain partial data, so we filter them out.
| where Timestamp > starttime + 28d
// render as timechart
| render timechart
```

この例では、次のように出力されます。

:::image type="content" source="images/samples/rolling-mau.png" alt-text="変化する月間ユーザーの出力":::

次の例では、上記のクエリを再利用可能な関数に変換し、それを使用してユーザーのロールのローリングを計算します。 このクエリのアクティブ ユーザーは、特定の日に少なくとも 1 回の精算を実行したユーザーのみと定義されています。

``` Kusto
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
    | mvexpand RollingUserClicksByDay
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

この例では、次のように出力されます。

:::image type="content" source="images/samples/user-stickiness.png" alt-text="ユーザーの持続性の出力":::

### <a name="regression-analysis"></a>回帰分析
この例は、アプリケーションのトレース ログのみに基づいてサービス中断の自動検出機能を作成する方法を示しています。 この検出機能で、アプリケーション内のエラーと警告のトレースの相対量が異常に急増した場合が検出されます。

トレース ログ データに基づいてサービスの状態を評価するために、2 つの手法が使用されています。

- [make-series](/azure/kusto/query/make-seriesoperator) を使用して、半構造化テキスト トレースログを、正および負のトレース線の比率を表すメトリックに変換します。
- [series_fit_2lines](/azure/kusto/query/series-fit-2linesfunction) と [series_fit_line](/azure/kusto/query/series-fit-linefunction) を使用して、2 本の線形回帰による時系列分析を使用して高度なステップジャンプ検出を実行します。

``` Kusto
let startDate = startofday(datetime("2017-02-01"));
let endDate = startofday(datetime("2017-02-07"));
let minRsquare = 0.8;  // Tune the sensitivity of the detection sensor. Values close to 1 indicate very low sensitivity.
// Count all Good (Verbose + Info) and Bad (Error + Fatal + Warning) traces, per day
traces
| where timestamp > startDate and timestamp < endDate
| summarize 
    Verbose = countif(severityLevel == 0),
    Info = countif(severityLevel == 1), 
    Warning = countif(severityLevel == 2),
    Error = countif(severityLevel == 3),
    Fatal = countif(severityLevel == 4) by bin(timestamp, 1d)
| extend Bad = (Error + Fatal + Warning), Good = (Verbose + Info)
// Determine the ratio of bad traces, from the total
| extend Ratio = (todouble(Bad) / todouble(Good + Bad))*10000
| project timestamp , Ratio
// Create a time series
| make-series RatioSeries=any(Ratio) default=0 on timestamp in range(startDate , endDate -1d, 1d) by 'TraceSeverity' 
// Apply a 2-line regression to the time series
| extend (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(RatioSeries)
// Find out if our 2-line is trending up or down
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(LineFit2)
// Check whether the line fit reaches the threshold, and if the spike represents an increase (rather than a decrease)
| project PatternMatch = iff(RSquare2 > minRsquare and Slope>0, "Spike detected", "No Match")
```


## <a name="next-steps"></a>次のステップ

- [Kusto クエリ言語に関するチュートリアルを](tutorial.md?pivots=azuremonitor)紹介します。


::: zone-end

