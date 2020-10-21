---
title: サンプル-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのサンプルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0db8c472ed3b23a1bf46f8fce9cbd38b0ca960b3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251026"
---
# <a name="samples"></a>サンプル

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
|Cancel|Manchester|4267667|2015-12-09T10:27:26.29|
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

:::image type="content" source="images/samples/040.png" alt-text="縦棒グラフのスクリーンショット。Y 軸の範囲は 0 ~ 50 です。10個の色分けされた列は、10個の場所のそれぞれの値を表します。"::: 

コードを追加して、簡単にサイズ設定されたビンで期間をカウントします。この例では、横棒グラフの設定により、をで除算して、 `1s` 時間帯を数値に変換します。 

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/050.png" alt-text="縦棒グラフのスクリーンショット。Y 軸の範囲は 0 ~ 50 です。10個の色分けされた列は、10個の場所のそれぞれの値を表します。":::

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

*目的:*: a と B の2つのデータセットがあります。B の各レコードについて、の前のイベントを検索します (つまり、"古い" が B よりも前の arg_max レコード)。 次のサンプルデータセットに予想される出力を次に示します。 

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
