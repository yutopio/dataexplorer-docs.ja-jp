---
title: Azure Data Explorer と Azure Monitor でのクエリのサンプル
description: この記事では、Azure Data Explorer と Azure Monitor に Kusto クエリ言語を使用する一般的なクエリと例について説明します。
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
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "95783520"
---
# <a name="samples-for-queries-for-azure-data-explorer-and-azure-monitor"></a>Azure Data Explorer と Azure Monitor でのクエリのサンプル

::: zone pivot="azuredataexplorer"

この記事では、Azure Data Explorer での一般的なクエリのニーズと、Kusto クエリ言語を使用してそれらを満たす方法について説明します。

## <a name="display-a-column-chart"></a>縦棒グラフを表示する

2 つ以上の列のプロジェクションを行い、グラフの x 軸および y 軸としてその列を使用するには:

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* 1 番目の列は x 軸になります。 数値、日時、または文字列を使用できます。 
* 表示するデータの量を制限するには、`where`、`summarize`、`top` を使用します。
* x 軸の順序を定義するには、結果を並べ替えます。

:::image type="content" source="images/samples/color-bar-chart.png" alt-text="10 個の場所の値を表す 10 色の列が含まれる縦棒グラフのスクリーンショット。":::

## <a name="get-sessions-from-start-and-stop-events"></a>開始および停止イベントからのセッションの取得

イベントのログで、一部のイベントに拡張アクティビティまたはセッションの開始または終了がマークされています。 

|名前|City|SessionId|Timestamp|
|---|---|---|---|
|開始|London|2817330|2015-12-09T10:12:02.32|
|Game|London|2817330|2015-12-09T10:12:52.45|
|開始|Manchester|4267667|2015-12-09T10:14:02.23|
|Stop|London|2817330|2015-12-09T10:23:43.18|
|キャンセル|Manchester|4267667|2015-12-09T10:27:26.29|
|Stop|Manchester|4267667|2015-12-09T10:28:31.72|

すべてのイベントには、セッション ID (`SessionId`) があります。 課題は、開始と停止のイベントをセッション ID と一致させることです。

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

開始と停止のイベントをセッション ID と一致させるには:

1. 結合を始める前に、[let](./letstatement.md) を使用して、可能な限り減らすテーブルのプロジェクションを指定します。
1. [project](./projectoperator.md) を使用して、開始時刻と停止時刻の両方が結果で表示されるように、タイムスタンプの名前を変更します。 また、`project` を使用して結果に表示する他の列を選択します。 
1. [join](./joinoperator.md) を使用して、開始と停止のエントリを同じアクティビティに一致させます。 アクティビティごとに、1 つの行が作成されます。 
1. 再び `project` を使用して、アクティビティの期間を示すための列を追加します。

出力は次のようになります。

|City|SessionId|StartTime|StopTime|Duration|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Manchester|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

## <a name="get-sessions-without-using-a-session-id"></a>セッション ID を使用しないでセッションを取得する

不便なことに、開始および停止イベントにマッチングできるセッション ID がないものとします。 ただし、セッションが行われたクライアントの IP アドレスはあります。 各クライアントのアドレスでは一度に 1 つのセッションしか行われないとすると、各開始イベントを同じ IP アドレスからの次の停止イベントにマッチングできます。

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

`join` により、同じクライアントの IP アドレスからのすべての開始時刻とすべての停止時刻のマッチングが行われます。 サンプル コード:

- 前の停止時刻との一致を削除します。
- 開始時刻と IP アドレスでグループ化して、各セッションのグループを取得します。 
- `StartTime` パラメーターに `bin` 関数を使用します。 このステップを行わないと、Kusto により 1 時間のビンが自動的に使用されて、一部の開始時刻が間違った停止時刻とマッチングされます。

`arg_min` により各グループで期間が最も小さい行が検索され、`*` パラメーターが他のすべての列に渡されます。 

引数で各列名の前に `min_` が付加されます。 

:::image type="content" source="images/samples/start-stop-ip-address-table.png" alt-text="クライアントと開始時刻の組み合わせごとに開始時刻、クライアント IP、期間、都市、最早停止の列が含まれる、結果の一覧が表示されたテーブルのスクリーンショット。"::: 

便利なサイズのビンで期間をカウントするコードを追加します。この例では、棒グラフの方が見やすいため、`1s` 単位で期間を数値に変換します。

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/number-of-sessions-bar-chart.png" alt-text="指定した範囲の期間でセッションの数を示す縦棒グラフのスクリーンショット。":::

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

アクティビティとその開始および終了時刻のテーブルがあるとします。 同時に実行されたアクティビティの数を経時的に示すグラフを表示できます。

`X` というサンプル入力を次に示します。

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

ビンが 1 分間のグラフの場合は、実行中の各アクティビティを 1 分間隔でカウントします。

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

それらの配列を保持する代わりに、[mv-expand](./mvexpandoperator.md) を使用して展開します。

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

これで、サンプル時間で結果をグループ化し、アクティビティごとの発生回数をカウントできます。

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* [mv-expand](./mvexpandoperator.md) の結果は dynamic 型の列であるため、`todatetime()` を使用します。
* 数値や日付の場合、間隔を指定しないと、`summarize` により常に既定の間隔を使用して `bin()` 関数が適用されるため、`bin()` を使用します。 

出力は次のようになります。

| count_SessionId | サンプル|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

棒グラフまたは時間グラフを使用して、結果をレンダリングできます。

## <a name="introduce-null-bins-into-summarize"></a>*summarize* に null ビンを導入する

日付と時刻の列で構成されるグループ キーに `summarize` 演算子を適用すると、それらの値は固定幅のビンに分割されます。

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

この例では、5 分の各ビンに分類される `T` 内の行のグループごとに 1 行が作成されるテーブルが生成されます。

このコードでは、"null ビン" は追加されません。これは、`StartTime` と `StopTime` の間の時間ビン値のうち、`T` に対応する行がないものの行です。 これは、それらのビンでテーブルを "埋める" のによい方法です。そのための 1 つの方法は次のとおりです。

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

上記のクエリの詳細な手順の説明を次に示します。 

1. `union` 演算子を使用して、テーブルに行を追加します。 それらの行は、`union` 式によって生成されます。
1. `range` 演算子により、1 つの行と列を含むテーブルが生成されます。 そのテーブルは、`mv-expand` を機能させるため以外には使用されません。
1. `range` 関数に対する `mv-expand` 演算子により、`StartTime` と `EndTime` の間に 5 分ビンと同じ数の行が作成されます。
1. `Count` には `0` を使用します。
1. `summarize` 演算子により、元の引数 (左、つまり外側) から `union` にビンがグループ化されます。 また、演算子により、内部引数からそれへのビン分割も行われます (null ビン行)。 このプロセスにより、出力には、ビンごとに 0 個または元の数の値を持つ 1 つの行が含まれるようになります。

## <a name="get-more-from-your-data-by-using-kusto-with-machine-learning"></a>Kusto と機械学習を使用してデータをさらに活用する 

機械学習アルゴリズムを使用してテレメトリ データから興味深い分析情報を導き出す面白いユース ケースが数多くあります。 多くの場合、これらのアルゴリズムには、入力として厳密に構造化されたデータセットが必要です。 通常、生のログ データは、必要な構造とサイズと一致しません。 

最初に、特定の Bing 推論サービスのエラー率で異常を調べます。 ログ テーブルには 650 億件のレコードがあります。 次の基本的なクエリによって 25 万件のエラーがフィルター処理された後、異常検出関数 [series_decompose_anomalies](series-decompose-anomaliesfunction.md) を使用してエラー数の時系列が作成されます。 Kusto サービスによって検出された異常は、時系列グラフで赤い点として強調表示されます。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

サービスにより、エラー率が疑わしい時間バケットがいくつか特定されました。 Kusto を使用して、この期間を拡大します。 次に、`Message` 列で集計を行うクエリを実行します。 上位のエラーを検索してみてください。 

メッセージのスタック トレース全体の関連する部分が抽出されるため、結果はページにより良く収まるようになります。 

上位 8 個のエラーが正しく識別されていることを確認できます。 しかし、その次には長い一連のエラーがあります。これは、変化するデータを含む書式指定文字列を使用してエラー メッセージが作成されたためです。

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
|7125|メソッド 'RunCycleFromInterimData' の ExecuteAlgorithmMethod が失敗しました...
|7125|InferenceHostService の呼び出しが失敗しました..System.NullReferenceException:オブジェクト参照がオブジェクト インスタンスに設定されていません...
|7124|予期しない推論システム エラー..System.NullReferenceException:オブジェクト参照がオブジェクト インスタンスに設定されていません... 
|5112|予期しない推論システム エラー..System.NullReferenceException:オブジェクト参照がオブジェクト インスタンスに設定されていません..
|174|InferenceHostService の呼び出しが失敗しました..System.ServiceModel.CommunicationException:パイプへの書き込みでエラーが発生しました:...
|10|メソッド 'RunCycleFromInterimData' の ExecuteAlgorithmMethod が失敗しました...
|10|推論システム エラー..Microsoft.Bing.Platform.Inferences.Service.Managers.UserInterimDataManagerException:...
|3|InferenceHostService の呼び出しが失敗しました..System.ServiceModel.CommunicationObjectFaultedException:...
|1|推論システム エラー...SocialGraph.BOSS.OperationResponse...AIS TraceId:8292FC561AC64BED8FA243808FE74EFD...
|1|推論システム エラー...SocialGraph.BOSS.OperationResponse...AIS TraceId:5F79F7587FF943EC9B641E02E701AFBF...

このようなときは、`reduce` 演算子を使用すると役に立ちます。 この演算子により、コード内の同じトレース インストルメンテーション ポイントで発生した 63 個の異なるエラーが識別されました。 `reduce` を使用すると、その時間枠で意味のある追加のエラー トレースに焦点を当てることができます。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| reduce by Message with threshold=0.35
| project Count, Pattern
```

|Count|Pattern
|---|---
|7125|メソッド 'RunCycleFromInterimData' の ExecuteAlgorithmMethod が失敗しました...
|  7125|InferenceHostService の呼び出しが失敗しました..System.NullReferenceException:オブジェクト参照がオブジェクト インスタンスに設定されていません...
|  7124|予期しない推論システム エラー..System.NullReferenceException:オブジェクト参照がオブジェクト インスタンスに設定されていません...
|  5112|予期しない推論システム エラー..System.NullReferenceException:オブジェクト参照がオブジェクト インスタンスに設定されていません...
|  174|InferenceHostService の呼び出しが失敗しました..System.ServiceModel.CommunicationException:パイプへの書き込みでエラーが発生しました:...
|  63|推論システム エラー..Microsoft.Bing.Platform.Inferences.\*:書き込み \* オブジェクト BOSS への書き込み。\*:SocialGraph.BOSS.Reques...
|  10|メソッド 'RunCycleFromInterimData' の ExecuteAlgorithmMethod が失敗しました...
|  10|推論システム エラー..Microsoft.Bing.Platform.Inferences.Service.Managers.UserInterimDataManagerException:...
|  3|InferenceHostService の呼び出しが失敗しました..System.ServiceModel.\*:\*\* に対するオブジェクト System.ServiceModel.Channels.\*+\* が \*... Syst...

これで、検出された異常の一因になった上位のエラーを確認しやすくなりました。

サンプル システムでのこれらのエラーの影響を理解するには、次のことを考慮します。 
* `Logs` テーブルには、`Component` や `Cluster` などの追加のディメンション データが含まれています。
* 新しい autocluster プラグインは、簡単なクエリを使用してコンポーネントとクラスターの分析情報を得るのに役立ちます。 

次の例では、上位 4 つのエラーのそれぞれがコンポーネントに固有であることがわかります。 また、上位 3 つのエラーは DB4 クラスターに固有ですが、4 番目のエラーはすべてのクラスターで発生します。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|Count |パーセンテージ (%)|コンポーネント|クラスター|Message
|---|---|---|---|---
|7125|26.64|InferenceHostService|DB4|ExecuteAlgorithmMethod for method...
|7125|26.64|不明なコンポーネント|DB4|InferenceHostService の呼び出しが失敗しました...
|7124|26.64|InferenceAlgorithmExecutor|DB4|予期しない推論システム エラー...
|5112|19.11|InferenceAlgorithmExecutor|*|予期しない推論システム エラー...

## <a name="map-values-from-one-set-to-another"></a>あるセットから別のセットに値をマップする

一般的なクエリのユース ケースは、値の静的なマッピングです。 静的なマッピングを使用すると、結果の体裁をよくするのに役立ちます。

たとえば、次の表では、`DeviceModel` によってデバイス モデルが指定されています。 デバイス モデルを使用するのは、デバイス名を参照するのに便利な形式ではありません。  

|DeviceModel |Count 
|---|---
|iPhone5,1 |32 
|iPhone3,2 |432 
|iPhone7,2 |55 
|iPhone5,2 |66 

 フレンドリ名を使用する方が便利です。  

|FriendlyName |Count 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

次の 2 つの例では、デバイスの識別に使用するものをデバイス モデルからフレンドリ名に変更する方法を示します。  

### <a name="map-by-using-a-dynamic-dictionary"></a>動的ディクショナリを使用してマップする

動的ディクショナリと動的アクセサーを使用して、マッピングを実現できます。 次に例を示します。

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

永続的なテーブルと `join` 演算子を使用して、マッピングを行うこともできます。
 
1. マッピング テーブルを 1 回だけ作成します。

    ```kusto
    .create table Devices (DeviceModel: string, FriendlyName: string) 

    .ingest inline into table Devices 
        ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
    ```

1. デバイス コンテンツのテーブルを作成します。

    |DeviceModel |FriendlyName 
    |---|---
    |iPhone5,1 |iPhone 5 
    |iPhone3,2 |iPhone 4 
    |iPhone7,2 |iPhone 6 
    |iPhone5,2 |iPhone5 

1. テスト テーブル ソースを作成します。

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
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 


## <a name="create-and-use-query-time-dimension-tables"></a>クエリ時間ディメンション テーブルを作成して使用する

クエリの結果を、データベースに格納されていないアドホックなディメンション テーブルと結合したいことがよくあります。 結果が 1 つのクエリを対象とするテーブルになる式を定義できます。 

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

## <a name="retrieve-the-latest-records-by-timestamp-per-identity"></a>ID ごとに (タイムスタンプで) 最新のレコードを取得する

次のものが含まれるテーブルがあるとします。
* 各行が関連付けられているエンティティを識別する `ID` 列 (ユーザー ID やノード ID など)
* 行の時刻参照を提供する `timestamp` 列
* 他の列

[top-nested 演算子](topnestedoperator.md)を使用すると、`ID` 列の各値について最新の 2 つのレコードを返すクエリを作成できます。"_最新_" は、" _`timestamp` の値が最も高いもの_" と定義されます。

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


以下では、上記のクエリの詳細な手順を説明します (番号は、コードのコメント内の番号を示します)。

1. `datatable` は、デモンストレーション用のテスト データを生成する方法です。 通常、ここでは実際のデータを使用します。
1. この行は基本的に、" _`id` のすべての個別の値を返す_" ことを意味します。 
1. 次に、この行によって、次の値が最大になる上位 2 つのレコードが返されます。
     * `timestamp` 列
     * 前のレベルの列 (ここでは `id`)
     * このレベルで指定されている列 (ここでは `timestamp`)
1. この行により、前のレベルで返された各レコードに対する `bla` 列の値が追加されます。 テーブルの他の列に関心がある場合は、それらの列ごとにこの行を繰り返すことができます。
1. 最後の行では、[project-away 演算子](projectawayoperator.md)を使用して、`top-nested` によって取り込まれた "余分な" 列を削除します。

## <a name="extend-a-table-by-a-percentage-of-the-total-calculation"></a>合計計算の割合でテーブルを拡張する

数値列が含まれる表形式の式は、合計に対する割合として値が指定されていると、ユーザーにとっていっそう便利です。

たとえば、クエリによって次のテーブルが生成されるとします。

|SomeSeries|SomeInt|
|----------|-------|
|Apple       |    100|
|Banana       |    200|

次のようなテーブルの方が望ましい表示です。

|SomeSeries|SomeInt|Pct |
|----------|-------|----|
|Apple       |    100|33.3|
|Banana       |    200|66.6|

テーブルの表示方法を変更するには、`SomeInt` 列の合計 (sum) を計算してから、この列の各値を合計で除算します。 任意の結果を得るには、[as 演算子](asoperator.md)を使用します。

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

## <a name="perform-aggregations-over-a-sliding-window"></a>スライディング ウィンドウの集計を実行する

次の例では、スライディング ウィンドウを使用することにより列を集計する方法を示します。 このクエリでは、タイムスタンプごとに果物の価格が含まれる次のテーブルを使用します。

7 日のスライディング ウィンドウを使用して、各果物の 1 日ごとの最小、最大、合計のコストを計算します。 結果セットの各レコードには前の 7 日間が集計されており、結果には分析期間の日ごとのレコードが含まれます。

果物テーブル:

|Timestamp|Fruit|Price|
|---|---|---|
|2018-09-24 21:00:00.0000000|バナナ|3|
|2018-09-25 20:00:00.0000000|Apples|9|
|2018-09-26 03:00:00.0000000|バナナ|4|
|2018-09-27 10:00:00.0000000|Plums|8|
|2018-09-28 07:00:00.0000000|バナナ|6|
|2018-09-29 21:00:00.0000000|バナナ|8|
|2018-09-30 01:00:00.0000000|Plums|2|
|2018-10-01 05:00:00.0000000|バナナ|0|
|2018-10-02 02:00:00.0000000|バナナ|0|
|2018-10-03 13:00:00.0000000|Plums|4|
|2018-10-04 14:00:00.0000000|Apples|8|
|2018-10-05 05:00:00.0000000|バナナ|2|
|2018-10-06 08:00:00.0000000|Plums|8|
|2018-10-07 12:00:00.0000000|バナナ|0|

スライディング ウィンドウの集計クエリを次に示します。 クエリ結果の後の説明を参照してください。

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
|2018-10-01 00:00:00.0000000|Apples|9|9|9|
|2018-10-01 00:00:00.0000000|バナナ|0|8|18|
|2018-10-01 00:00:00.0000000|Plums|2|8|10|
|2018-10-02 00:00:00.0000000|バナナ|0|8|18|
|2018-10-02 00:00:00.0000000|Plums|2|8|10|
|2018-10-03 00:00:00.0000000|Plums|2|8|14|
|2018-10-03 00:00:00.0000000|バナナ|0|8|14|
|2018-10-04 00:00:00.0000000|バナナ|0|8|14|
|2018-10-04 00:00:00.0000000|Plums|2|4|6|
|2018-10-04 00:00:00.0000000|Apples|8|8|8|
|2018-10-05 00:00:00.0000000|バナナ|0|8|10|
|2018-10-05 00:00:00.0000000|Plums|2|4|6|
|2018-10-05 00:00:00.0000000|Apples|8|8|8|
|2018-10-06 00:00:00.0000000|Plums|2|8|14|
|2018-10-06 00:00:00.0000000|バナナ|0|2|2|
|2018-10-06 00:00:00.0000000|Apples|8|8|8|
|2018-10-07 00:00:00.0000000|バナナ|0|2|2|
|2018-10-07 00:00:00.0000000|Plums|4|8|12|
|2018-10-07 00:00:00.0000000|Apples|8|8|8|

このクエリでは、入力テーブル内の各レコードが、実際に発生した日より後の 7 日にわたって "拡張" (複製) されます。 各レコードは実際には 7 回出現します。 その結果、日単位の集計には、過去 7 日間のすべてのレコードが含まれます。


上記のクエリの詳細な手順の説明を次に示します。 

1. 各レコードを 1 日にビン分割します (`_start`)。 
1. レコードごとに範囲の終わりを決定します。値が `_start` から `_end` までの範囲外でない限り `_bin + 7d` に設定され、範囲外の場合は調整されます。 
1. 各レコードについて、現在のレコードの日から始まる 7 日間の配列が作成されます (タイムスタンプ)。 
1. `mv-expand` により、配列の各レコードが 1 日ずつずらして 7 つのレコードに複製されます。 
1. 日ごとに集計関数が実行されます。 #4 のため、このステップでは実際には "_過去_" 7 日間が集計されます。 
1. 最初の 7 日間については遡る 7 日の期間がないため、最初の 7 日間のデータは不完全です。 最初の 7 日間は、最終結果から除外されます。 この例では、それらは 2018-10-01 の集計にのみ含まれます。

## <a name="find-the-preceding-event"></a>前のイベントを検索する

次の例では、2 つのデータセット間で前のイベントを検索する方法を示します。  

A と B の 2 つのデータセットがあります。データセット B の各レコードについて、前のイベントをデータセット A で見つけます (つまり、B より "_古い_" A の `arg_max` レコード)。

サンプルのデータセットを次に示します。 

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
|2019-01-01 00:00:00.0000000|x|Ax1|
|2019-01-01 00:00:00.0000000|z|Az1|
|2019-01-01 00:00:01.0000000|x|Ax2|
|2019-01-01 00:00:02.0000000|○|Ay1|
|2019-01-01 00:00:05.0000000|○|Ay2|

</br>

|Timestamp|ID|EventA|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|B|
|2019-01-01 00:00:04.0000000|x|B|
|2019-01-01 00:00:04.0000000|○|B|
|2019-01-01 00:02:00.0000000|z|B|

予想される出力: 

|ID|Timestamp|EventB|A_Timestamp|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|○|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:02.0000000|Ay1|
|z|2019-01-01 00:02:00.0000000|B|2019-01-01 00:00:00.0000000|Az1|

この問題には、2 つの異なる方法が推奨されます。 特定のデータセットで両方をテストし、シナリオに最適なものを見つけることができます。

> [!NOTE] 
> 各方法がどのように実行されるかは、データセットによって異なる場合があります。

### <a name="approach-1"></a>方法 1

この方法では、ID とタイムスタンプを使用して両方のデータセットをシリアル化します。 次に、データセット B 内のすべてのイベントを、それらに先行するデータセット A 内のすべてのイベントでグループ化します。最後に、グループ内のデータセット A のすべてのイベントから `arg_max` を取得します。

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

### <a name="approach-2"></a>方法 2

この方法で問題を解決するには、最大遡及期間が必要です。 この方法では、データセット B と比較して、データセット A 内のレコードがどれくらい "_古い_" かを調べます。次に、ID とこの遡及期間に基づいて、2 つのデータセットを結合します。

`join` により、可能性のあるすべての候補、つまりデータセット B のレコードより古く、遡及期間内である、データセット A のすべてのレコードが生成されます。 次に、データセット B に最も近いものが、`arg_min (TimestampB - TimestampA)` によってフィルター処理されます。 遡及期間が短いほど、クエリの結果は向上します。

次の例では、遡及期間は `1m` に設定されています。 ID `z` のレコードには、対応する `A` イベントがありません。これは、その `A` イベントが 2 分だけ古いためです。

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
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|○|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|B|Ay1|
|z|2019-01-01 00:02:00.0000000||B||


## <a name="next-steps"></a>次のステップ

- [Kusto クエリ言語に関するチュートリアル](tutorial.md?pivots=azuredataexplorer)に目を通します。

::: zone-end

::: zone pivot="azuremonitor"

この記事では、Azure Monitor での一般的なクエリのニーズと、Kusto クエリ言語を使用してそれらを満たす方法について説明します。

## <a name="string-operations"></a>文字列操作

以下のセクションでは、Kusto クエリ言語を使用するときの文字列の操作方法の例を説明します。

### <a name="strings-and-how-to-escape-them"></a>文字列とそれをエスケープする方法

文字列の値は、一重引用符または二重引用符のいずれかで囲まれています。 文字をエスケープするには、文字の左側に円記号 (\\) を追加します。タブの場合は `\t`、改行の場合は `\n`、一重引用符文字の場合は `\"` です。

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

演算子       |[説明]                         |大文字と小文字を区別する|例 (`true` になる)
---------------|------------------------------------|--------------|-----------------------
`==`           |等しい                              |はい           |`"aBc" == "aBc"`
`!=`           |等しくない                          |はい           |`"abc" != "ABC"`
`=~`           |等しい                              |いいえ            |`"abc" =~ "ABC"`
`!~`           |等しくない                          |いいえ            |`"aBc" !~ "xyz"`
`has`          |右側の値が左側の値に完全な用語として含まれる |いいえ|`"North America" has "america"`
`!has`         |右側の値が左側の値に完全な用語として含まれない       |いいえ            |`"North America" !has "amer"` 
`has_cs`       |右側の値が左側の値に完全な用語として含まれる |はい|`"North America" has_cs "America"`
`!has_cs`      |右側の値が左側の値に完全な用語として含まれない       |はい            |`"North America" !has_cs "amer"` 
`hasprefix`    |右側の値が左側の値の用語のプレフィックスとして含まれる         |いいえ            |`"North America" hasprefix "ame"`
`!hasprefix`   |右側の値が左側の値の用語のプレフィックスとして含まれない     |いいえ            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |右側の値が左側の値の用語のプレフィックスとして含まれる         |はい            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |右側の値が左側の値の用語のプレフィックスとして含まれない     |はい            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |右側の値が左側の値の用語のサフィックスとして含まれる         |いいえ            |`"North America" hassuffix "ica"`
`!hassuffix`   |右側の値が左側の値の用語のサフィックスとして含まれない     |いいえ            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |右側の値が左側の値の用語のサフィックスとして含まれる         |はい            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |右側の値が左側の値の用語のサフィックスとして含まれない     |はい            |`"North America" !hassuffix_cs "icA"`
`contains`     |右側の値が左側の値のサブシーケンスとして出現する  |いいえ            |`"FabriKam" contains "BRik"`
`!contains`    |右側の値が左側の値に出現しない           |いいえ            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |右側の値が左側の値のサブシーケンスとして出現する  |はい           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |右側の値が左側の値に出現しない           |はい           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |右側の値が左側の値の先頭のサブシーケンスである|いいえ            |`"Fabrikam" startswith "fab"`
`!startswith`  |右側の値が左側の値の先頭のサブシーケンスではない|いいえ        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |右側の値が左側の値の先頭のサブシーケンスである|はい            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |右側の値が左側の値の先頭のサブシーケンスではない|はい        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |右側の値が左側の値の末尾のサブシーケンスである|いいえ             |`"Fabrikam" endswith "Kam"`
`!endswith`    |右側の値が左側の値の末尾のサブシーケンスではない|いいえ         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |右側の値が左側の値の末尾のサブシーケンスである|はい             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |右側の値が左側の値の末尾のサブシーケンスではない|はい         |`"Fabrikam" !endswith "brik"`
`matches regex`|左側の値に右側の値と一致するものが含まれる|はい           |`"Fabrikam" matches regex "b.*k"`
`in`           |要素のいずれかに等しい       |はい           |`"abc" in ("123", "345", "abc")`
`!in`          |要素のいずれとも等しくない   |はい           |`"bca" !in ("123", "345", "abc")`


### <a name="countof"></a>*countof*

文字列内の部分文字列の出現回数をカウントします。 プレーン文字列と一致すること、または正規表現を使用することができます。 プレーン文字列の一致は重複する可能性がありますが、正規表現の一致は重複しません。

```
countof(text, search [, kind])
```

- `text`:入力文字列 
- `search`:text 内で一致するプレーン文字列または正規表現
- `kind`: _normal_ | _regex_ (既定値: normal)。

コンテナー内で検索文字列が一致する回数が返されます。 プレーン文字列の一致は重複する可能性がありますが、正規表現の一致は重複しません。

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

特定の文字列から正規表現との一致を取得します。 必要に応じて、抽出された部分文字列を、指定した型に変換できます。

```kusto
extract(regex, captureGroup, text [, typeLiteral])
```

- `regex`:正規表現。
- `captureGroup`:抽出するキャプチャ グループを示す正の整数の定数。 一致全体の場合は 0、正規表現の最初のかっこ \(\) で囲まれた部分と一致した値の場合は 1、後続のかっこの場合は 2 以上を使用します。
- `text` - 検索対象の文字列。
- `typeLiteral` - オプションの type リテラル (例: `typeof(long)`)。 指定した場合、抽出された部分文字列はこの型に変換されます。

指定したキャプチャ グループ `captureGroup` に対して一致した部分文字列が、必要に応じて `typeLiteral` に変換されて返されます。 一致がないか、型変換が失敗した場合は、null 値が返されます。

次の例では、ハートビート レコードから `ComputerIP` の最終オクテットを抽出します。

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

次の例では、最終オクテットを抽出して *real* 型 (数値) にキャストした後、次の IP 値を計算します

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

次の例では、文字列 `Trace` で `Duration` の定義を検索します。 一致は `real` にキャストされ、時間定数 (1 s) で乗算された後、`Duration` が `timespan` 型にキャストされます。

```kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


### <a name="isempty-isnotempty-notempty"></a>*isempty*、*isnotempty*、*notempty*

- 引数が空の文字列または null である場合、`isempty` からは `true` が返されます (`isnull` を参照)。
- 引数が空の文字列または null でない場合、`isnotempty` からは `true` が返されます (`isnotnull` を参照)。 別名: `notempty`。


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

URL をパーツ (プロトコル、ホスト、ポートなど) に分割し、そのパーツが文字列として含まれるディクショナリ オブジェクトを返します。

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

- `regex`:照合する正規表現。 複数のキャプチャ グループをかっこ \(\) 内に含めることができます。
- `rewrite`:照合する正規表現と一致したものすべてが置き換えられた後の正規表現。 完全一致を参照する場合は \0、最初のキャプチャ グループの場合は \1、後続のキャプチャ グループの場合は \2 などを使用します。
- `input_text`:検索場所を示す入力文字列。

regex とのすべての一致が rewrite の評価で置き換えられた後のテキストが返されます。 一致が重複することはありません。

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

特定の文字列が、指定した区切り記号に従って分割され、結果の部分文字列の配列が返されます。

```
split(source, delimiter [, requestedIndex])
```

- `source`:指定した区切り記号に従って分割する文字列。
- `delimiter`:ソース文字列の分割に使用する区切り記号。
- `requestedIndex`:0 から始まるインデックス (省略可能)。 指定した場合、返される文字列配列にはその項目のみが含まれます (存在する場合)。


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

特定のソース文字列から、指定したインデックス位置以降にある部分文字列が抽出されます。 必要に応じて、要求する部分文字列の長さを指定できます。

```
substring(source, startingIndex [, length])
```

- `source`:部分文字列の取得元となるソース文字列。
- `startingIndex`:要求する部分文字列の、0 から始まる開始文字位置。
- `length`:要求する部分文字列に対して返される文字数の指定に使用できる省略可能なパラメーター。

#### <a name="example"></a>例

```kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


### <a name="tolower-toupper"></a>*tolower*、*toupper*

特定の文字列をすべて小文字またはすべて大文字に変換します。

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

以下のセクションでは、Kusto クエリ言語を使用するときの日付と時刻の値の操作方法の例を説明します。

### <a name="date-time-basics"></a>日時の基本

Kusto クエリ言語では、主要な 2 つのデータ型 (`datetime` と `timespan`) が日付と時刻に関連付けられています。 日付はすべて UTC で表されています。 複数の datetime 形式がサポートされていますが、ISO-8601 形式をお勧めします。 

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

`todatetime` 演算子を使用して文字列をキャストすることにより、日付と時刻の値を作成できます。 たとえば、特定の期間に送信される VM ハートビートを確認するには、`between` 演算子を使用して時間範囲を指定します。

```kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

日時値を現在と比較するシナリオも一般的です。 たとえば、過去 2 分間のすべてのハートビートを確認するには、`now` 演算子と 2 分を示す timespan を一緒に使用します。

```kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

この関数にはショートカットも使用できます。

```kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

最も短く読みやすいのは、`ago` 演算子を使用する方法です。

```kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

たとえば、開始時刻と終了時刻ではなく、開始時刻と期間がわかっているとします。 クエリは次のように書き換えることができます。

```kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

### <a name="convert-time-units"></a>時間単位の変換

datetime または timespan の値を既定以外の時間単位で表したい場合があります。 たとえば、過去 30 分間のエラー イベントを確認していて、イベントがどのくらい前に発生したかを示す計算列が必要な場合は、次のクエリを使用できます。

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

`timeAgo` 列には `00:09:31.5118992` のような値が保持されており、hh:mm:ss.fffffff として書式設定されています。 これらの値を、開始時刻からの時間 (分) の `number` に設定する場合は、その値を `1m` で除算します。

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```

### <a name="aggregations-and-bucketing-by-time-intervals"></a>集計と期間でのバケット

さらに、特定の時間単位で、特定の期間にわたる統計値を取得しなければならないシナリオもよく見られます。 このシナリオの場合、`summarize` 句の一部として `bin` 演算子を使用できます。

次のクエリを使用すると、過去 30 分間に発生したイベント数を 5 分ごとに取得できます。

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

`startofday` などの関数を使用して、結果のバケットを作成することもできます。

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

すべての日時値が UTC で表されるため、多くの場合、これらの値をローカル タイム ゾーンに変換すると便利です。 たとえば、次の計算を使用すると、UTC が PST 時間に変換されます。

```kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="aggregations"></a>集計

以下のセクションでは、Kusto クエリ言語を使用するときにクエリの結果を集計する方法の例を説明します。

### <a name="count"></a>*count*

任意のフィルターが適用された結果セット内の行数をカウントします。 次の例では、過去 30 分間にわたる `Perf` テーブル内の行数の合計が返されます。 結果は、`count_` という名前の列で返されます (特定の名前を列に割り当てた場合を除く)。


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

時間グラフの視覚化は、時間の経過に沿った傾向を確認するうえで役立ちます。

```kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

この例の出力には、`Perf` レコード数のトレンド ラインが 5 分間隔で示されています。


:::image type="content" source="images/samples/perf-count-line-chart.png" alt-text="Perf レコード数のトレンド ラインが 5 分間隔で示されている折れ線グラフのスクリーンショット。":::

### <a name="dcount-dcountif"></a>*dcount*、*dcountif*

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

ご自身のデータのサブグループでカウントまたはその他の集計を実行するには、`by` キーワードを使用します。 たとえば、ハートビートを送信した個別の Linux コンピューターの数を国や地域ごとにカウントするには、次のクエリを使用します。

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


ご自身のデータのさらに小さなサブグループを分析するには、列名を `by` セクションに追加します。 たとえば、オペレーティング システムの種類 (`OSType`) あたりの個別のコンピューターの数を国や地域ごとにカウントできます。

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

さまざまなパーセンタイルを指定して、それぞれの集計結果を取得することもできます。

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

結果で、いくつかのコンピューター CPU の中央値が類似していることが示される場合があります。 ただし、CPU が中央値付近で安定しているコンピューターもあれば、極端に低い、または高い CPU 値が報告される場合もあります。 高い値や低い値は、コンピューターでスパイクが発生していることを意味します。

### <a name="variance"></a>Variance

値の分散を直接評価するには、標準偏差法と分散法を使用します。

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

CPU 使用率の安定性を分析する場合は、`stdev` と中央値の計算を組み合わせることをお勧めします。

```kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

### <a name="generate-lists-and-sets"></a>リストとセットの生成

`makelist` を使用して、特定の列にある値の順序でデータをピボットできます。 たとえば、コンピューターで行われる最も一般的な注文イベントを調査できます。 基本的に言って、各コンピューターの `EventID` 値の順番でデータをピボットできます。 

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

`makelist` は、データが渡された順序でリストを生成します。 イベントを一番前のものから最新のものに並べ替えるには、`order` ステートメントで `desc` の代わりに `asc` を使用します。 

個別の値だけの一覧を作成するのが便利な場合があります。 このリストは "_セット_" と呼ばれ、`makeset` コマンドを使用して生成できます。

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

`makelist` と同様に、`makeset` でも順序付けされたデータが処理されます。 `makeset` コマンドを使用すると、渡した行の順序に基づいて配列が生成されます。

### <a name="expand-lists"></a>リストの展開

`makelist` または `makeset` の逆の操作は `mv-expand` です。 `mv-expand` コマンドを使用すると、値のリストが別々の行に展開されます。 JSON や配列など、任意の数の動的な列に展開できます。 たとえば、過去 1 時間にハートビートを送信したコンピューターからデータを送信したソリューションの `Heartbeat` テーブルを確認できます。

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

`mv-expand` を使用すると、コンマ区切りリストではなく、次のようにそれぞれの値が個別の行に表示されます。

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


`makelist` を使用すると、項目をグループ化できます。 出力では、ソリューションごとにコンピューターのリストを表示できます。

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

### <a name="missing-bins"></a>不足しているビン

`mv-expand` の便利な適用方法の 1 つは、不足しているビンの既定値を埋めることです。たとえば、ハートビートを調べて、特定のコンピューターのアップタイムを確認しているとします。 また、`Category` 列に示されているハートビートのソースを確認する必要もあるとします。 通常は、基本的な `summarize` ステートメントを使用します。

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

出力には、"2017-06-06T19:00:00Z" に関連付けられたバケットはありません。これは、その時間のハートビート データがないためです。 `make-series` 関数を使用して、空のバケットに既定値を割り当てます。 カテゴリごとに行が生成されます。 出力には、追加の配列の列が 2 つ含まれます。1 つは値、1 つは合致する時間バケットです。

```kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

出力は次のようになります。

| カテゴリ | count_ | TimeGenerated |
|---|---|---|
| 直接エージェント | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

*count_* 配列の 3 番目の要素は、想定どおり 0 になります。 _TimeGenerated_ 配列には、一致するタイムスタンプ "2017-06-06T19:00:00.0000000Z" があります。 しかし、この配列形式は読み取るのが困難です。 `mv-expand` を使用して配列を展開し、`summarize` で生成されたものと同じ形式を出力します。

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



### <a name="narrow-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>要素のセットへの結果の絞り込み: *let*、*makeset*、*toscalar*、*in*

一般的なシナリオでは、基準のセットに基づいて特定のエンティティの名前を選択し、異なるデータ セットをフィルターして、そのエンティティのセットに絞り込みます。 たとえば、不足している更新プログラムがあることが認識されているコンピューターを見つけ、これらのコンピューターで呼び出された IP アドレスを特定することができます。

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

結合を使用すると、同じクエリで、複数のテーブルのデータを分析できます。 結合により、指定した列の値が照合され、2 つのデータ セットの行がマージされます。

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

この例の最初のデータセットでは、すべてのサインイン イベントがフィルター選択されます。 そのデータセットは、すべてのサインアウト イベントをフィルター選択する、2 番目のデータセットと結合されます。 予想列は `Computer`、`Account`、`TargetLogonId`、および `TimeGenerated` です。 データセットは、共有列 `TargetLogonId` で関連付けられています。 相関関係ごとに 1 つのレコードが出力されます。これにはサインイン時間とサインアウト時間の両方が含まれます。

両方のデータセットに同じ名前の列がある場合、右側のデータセットの列にインデックス番号が追加されます。 この例では、結果の `TargetLogonId` には左側のテーブルの値が、`TargetLogonId1` には右側のテーブルの値が示されています。 この場合、2 番目の `TargetLogonId1` 列は、`project-away` 演算子を使用して削除されました。

> [!NOTE]
> パフォーマンスを向上させるには、`project` 演算子を使用して、結合されたデータセットの関連する列のみを保持します。


次の構文を使用して、2 つのデータセットを結合します。結合されたキーの名前は 2 つのテーブルの間で異なります。

```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

### <a name="lookup-tables"></a>ルックアップ テーブル

結合の一般的な用途は、静的な値のマッピングに `datatable` を使用することです。 `datatable` を使用すると、結果の体裁をよくするのに役立ちます。 たとえば、セキュリティ イベント データを、各イベント ID のイベント名を使用してわかりやすくできます。

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
| オブジェクトへのハンドルが閉じられました | 290,995 |
| オブジェクトへのハンドルが要求されました | 154,157 |
| オブジェクトへのハンドルの複製が試みられました | 144,305 |
| オブジェクトへのアクセスが試行されました | 123,669 |
| 暗号化操作 | 153,495 |
| キー ファイル操作 | 153,496 |

## <a name="json-and-data-structures"></a>JSON とデータ構造

入れ子になったオブジェクトは、配列やキーと値のペアのマップ内に他のオブジェクトが含まれるオブジェクトです。 オブジェクトは JSON 文字列として表されます。 このセクションでは、JSON を使用してデータを取得し、入れ子になったオブジェクトを分析する方法を説明します。

### <a name="work-with-json-strings"></a>JSON 文字列の使用

既知のパスの特定の JSON 要素にアクセスするには、`extractjson` を使用します。 この関数では、次の規則を使用するパス式が必要です。

- ルート フォルダーを参照するには、 _$_ を使用します。
- 次の例に示すように、インデックスおよび要素を参照するために、角かっこまたはドット表記を使用します。


インデックスには角かっこを使用し、要素を区切るにはドットを使用します:

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

この例は似ていますが、角かっこ表記のみを使用しています。

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

要素が 1 つだけの場合は、ドット表記のみを使用できます。

```kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


### <a name="parsejson"></a>*parsejson*

動的オブジェクトとして JSON 構造内の複数の要素にアクセスするのが最も簡単です。 テキスト データを動的オブジェクトにキャストするには、`parsejson` を使用します。 JSON を動的な型に変換した後は、追加の関数を使用してデータを分析できます。

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

オブジェクトのプロパティを個別の行に分割するには、`mv-expand` を使用します。

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

結果は JSON 形式のスキーマです。

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

スキーマには、オブジェクト フィールドの名前と、それに対応するデータ型が記述されています。 

入れ子になったオブジェクトは、次の例のように、さまざまなスキーマがある場合があります。

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

## <a name="charts"></a>グラフ

以下のセクションでは、Kusto クエリ言語を使用するときのグラフの操作方法の例を説明します。

### <a name="chart-the-results"></a>結果をグラフ化する

まず、過去 1 時間のオペレーティング システムごとのコンピューター数を確認します。

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

既定では、結果はテーブルの形で表示されます。

:::image type="content" source="images/samples/query-results-table.png" alt-text="クエリ結果をテーブルで示すスクリーンショット。":::

さらに役に立つビューにするには、 **[グラフ]** を選択し、 **[円]** オプションを選択して、結果を視覚化します。

:::image type="content" source="images/samples/query-results-pie-chart.png" alt-text="クエリ結果を円グラフで示すスクリーンショット。":::

### <a name="timecharts"></a>時間グラフ

プロセッサ時間の平均、50 パーセンタイル、95 パーセンタイルを 1 時間のビンに表示します。 

次のクエリを実行すると、複数の系列が生成されます。 結果で、時間グラフに表示する系列を選択できます。

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

**[折れ線]** グラフの表示オプションを選択します。

:::image type="content" source="images/samples/multiple-series-line-chart.png" alt-text="複数系列の折れ線グラフを示すスクリーンショット。":::

#### <a name="reference-line"></a>基準線

基準線を使用すると、メトリックが特定のしきい値を超えているかどうかを簡単に識別できます。 グラフに線を追加するには、定数列を追加してデータセットを拡張します。

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

:::image type="content" source="images/samples/multiple-series-threshold-line-chart.png" alt-text="しきい値の基準線を持つ複数系列の折れ線グラフを示すスクリーンショット。":::


### <a name="multiple-dimensions"></a>複数のディメンション

`summarize` の`by` 句にある複数の式により、結果に複数の行が作成されます。 値の組み合わせごとに 1 つの行が作成されます。

```kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

結果をグラフとして表示するときは、`by` 句の最初の列がグラフに使用されます。 次の例では、`EventID` の値を使用して作成された積み上げ縦棒グラフを示します。 ディメンションは `string` 型である必要があります。 この例では、`EventID` の値が `string` にキャストされています。

:::image type="content" source="images/samples/select-column-chart-type-eventid.png" alt-text="EventID 列に基づく棒グラフを示すスクリーンショット。":::

列名のドロップダウン矢印を選択することにより、列を切り替えることができます。

:::image type="content" source="images/samples/select-column-chart-type-accounttype.png" alt-text="列セレクターが表示されている、AccountType 列に基づく棒グラフを示すスクリーンショット。":::

## <a name="smart-analytics"></a>スマート分析

このセクションでは、Azure Log Analytics のスマート分析機能を使用してユーザー アクティビティを分析する例を示します。 これらの例を使用して、Azure Application Insights によって監視されているアプリケーションを分析することができます。また、これらのクエリの概念を使用して、他のデータを同様に分析することもできます。 

### <a name="cohorts-analytics"></a>コーホート分析

コーホート分析では、特定のユーザー グループ ("_コーホート_" と呼ばれます) のアクティビティを追跡します。 コーホート分析を使用すると、ユーザーのリピート率を測定することで、サービスの魅力度が測られます。 ユーザーは、そのサービスを初めて使用した時間ごとにグループ化されます。 コーホートを分析する場合、最初の追跡対象期間でアクティビティが減少することが見込まれています。 各コーホートには、そのメンバーが初めて観察された週ごとにタイトルが付けられています。

次の例では、ユーザーがそのサービスを初めて使用した後の 5 週間に完了したアクティビティの数が分析されています。

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

:::image type="content" source="images/samples/cohorts-table.png" alt-text="アクティビティに基づくコーホートのテーブルを示すスクリーンショット。":::

### <a name="rolling-monthly-active-users-and-user-stickiness"></a>変化する月間アクティブ ユーザーとユーザーの持続性

次の例では、[series_fir](/azure/kusto/query/series-firfunction) 関数による時系列分析を使用します。 `series_fir` 関数は、スライディング ウィンドウの計算に使用できます。 監視対象のサンプル アプリケーションは、カスタム イベントを通じてユーザーのアクティビティを追跡するオンライン ストアです。 このクエリでは、`AddToCart` と `Checkout` の 2 種類のユーザー アクティビティが追跡されます。 アクティブなユーザーは、特定の日に少なくとも 1 回チェックアウトを完了したユーザーと定義されます。

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

:::image type="content" source="images/samples/rolling-monthly-active-users-chart.png" alt-text="1 か月にわたって日ごとに変化するアクティブなユーザーを示すグラフのスクリーンショット。":::

次の例は、前のクエリを再利用可能な関数に変換したものです。 この例では、クエリを使用して、変化するユーザーの持続性を計算します。 このクエリでのアクティブなユーザーは、特定の日に少なくとも 1 回チェックアウトを完了したユーザーと定義されます。

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

:::image type="content" source="images/samples/user-stickiness-chart.png" alt-text="経時的なユーザーの持続性を示すグラフのスクリーンショット。":::

### <a name="regression-analysis"></a>回帰分析

この例は、アプリケーションのトレース ログのみに基づいてサービス中断の自動検出機能を作成する方法を示しています。 この検出機能で、アプリケーション内のエラーと警告のトレースの相対量が異常に急増した場合が検出されます。

トレース ログ データに基づいてサービスの状態を評価するために、2 つの手法が使用されています。

- [make-series](/azure/kusto/query/make-seriesoperator) を使用して、半構造化テキスト トレースログを、正および負のトレース線の比率を表すメトリックに変換します。
- 2 本の線形回帰による時系列分析を使用することにより、高度なステップジャンプ検出に [series_fit_2lines](/azure/kusto/query/series-fit-2linesfunction) と [series_fit_line](/azure/kusto/query/series-fit-linefunction) を使用します。

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

- [Kusto クエリ言語に関するチュートリアル](tutorial.md?pivots=azuremonitor)に目を通します。


::: zone-end

