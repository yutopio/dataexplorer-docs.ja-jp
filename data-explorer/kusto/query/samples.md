---
title: サンプル - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのサンプルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 746017d41b5f1a13ce73f2c27df9cac5b5982ff0
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663592"
---
# <a name="samples"></a>サンプル

以下に、いくつかの一般的なクエリニーズと、Kusto クエリ言語を使用してクエリを実行する方法を示します。

## <a name="display-a-column-chart"></a>縦棒グラフを表示する

2 つ以上の列を投影し、グラフの x 軸と y 軸として使用します。

```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* 最初の列は x 軸を形成します。 数値、日時、または文字列を指定できます。 
* を`where``summarize`使用し`top`、表示するデータの量を制限します。
* X 軸の順序を定義するように結果を並べ替えます。

:::image type="content" source="images/samples/060.png" alt-text="060":::

<a name="activities"></a>
## <a name="get-sessions-from-start-and-stop-events"></a>開始および停止イベントからのセッションの取得

イベントのログがあり、一部のイベントで拡張アクティビティまたはセッションの開始または終了がマークされているとします。 

|名前|City|SessionId|Timestamp|
|---|---|---|---|
|[開始]|London|2817330|2015-12-09T10:12:02.32|
|Game|London|2817330|2015-12-09T10:12:52.45|
|[開始]|Manchester|4267667|2015-12-09T10:14:02.23|
|Stop|London|2817330|2015-12-09T10:23:43.18|
|Cancel|Manchester|4267667|2015-12-09T10:27:26.29|
|Stop|Manchester|4267667|2015-12-09T10:28:31.72|

すべてのイベントには SessionId があるため、問題は開始イベントと停止イベントを同じ ID に一致させる必要があります。

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

結合[`let`](./letstatement.md)に入る前に、できる限り下に置かれて表の投影法に名前を付けるために使用します。
[`Project`](./projectoperator.md)は、開始時刻と終了時刻の両方が結果に表示されるように、タイムスタンプの名前を変更するために使用されます。 結果に表示する他の列を選択することもできます。 [`join`](./joinoperator.md)同じアクティビティの開始エントリと終了エントリを一致させ、各アクティビティの行を作成します。 最後に、 `project` はもう一度列を追加して、アクティビティ期間を表示します。


|City|SessionId|StartTime|StopTime|Duration|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Manchester|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

### <a name="get-sessions-without-session-id"></a>セッション ID なしのセッションの取得

ここでは、不便なことに、開始および停止イベントにマッチングで使用できるセッション ID がないと仮定します。 ただし、セッションが行われたクライアントの IP アドレスはあります。 各クライアントのアドレスでは一度に 1 つのセッションしか行われないと仮定した場合、同じ IP アドレスから各開始イベントと次の停止イベントのマッチングを行うことができます。

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

結合では、同じクライアントの IP アドレスから各開始時刻とすべての停止時刻のマッチングを行います。 したがって、まず、前の停止時刻と一致した時刻を削除します。

次に、開始時刻と IP でグループ化し、各セッションのグループを取得します。 StartTime パラメーター`bin`の関数を指定する必要があります: そうでない場合、Kusto は自動的に 1 時間のビンを使用します。

`arg_min`各グループで最も短い期間の行が選択され、`*`パラメーターは他のすべての列を通過しますが、各列名に「min_」という接頭辞が付きます。 

:::image type="content" source="images/samples/040.png" alt-text="040"::: 

次に、便利なサイズのビンの期間をカウントするコードを追加できます。棒グラフの設定が少しずつなので、時間を数値`1s`に変換するために除算します。 


      // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 

:::image type="content" source="images/samples/050.png" alt-text="050":::

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

<a name="concurrent-activities"><a/>
## <a name="chart-concurrent-sessions-over-time"></a>同時セッションの時系列グラフ

開始および終了時刻を含むアクティビティのテーブルがあるとします。  ここでは、一定時間における同時実行数を示す時系列グラフを使用します。

ここでは、入力例を示`X`します。

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

1 分間のビンにグラフを入れたいので、1m 間隔ごとに、実行アクティビティごとにカウントできる何かを作成します。

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

しかし、これらの配列を保持する代わりに[、mv-expand](./mvexpandoperator.md)を使用してそれらを拡張します。

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

これで、アクティビティごとの発生回数をカウントして、サンプル時間でグループ化できます。

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* [mv-expand](./mvexpandoperator.md)は動的型の列を生成するため、todatetime() が必要です。
* 数値や日付の場合、間隔を指定しないと集計では常に既定の間隔で bin 関数が適用されるため、bin() が必要になります。 


| count_SessionId | サンプル|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

これは、棒グラフまたは時間グラフとして表示できます。

## <a name="introduce-null-bins-into-summarize"></a>ヌル・ビンを要約に導入する

`datetime`演算子が`summarize`列で構成されるグループ キーに適用されると、通常は、それらの値が固定幅ビンに "ビン" されます。例えば：

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

この操作では、5 分の各ビンに`T`含まれる行のグループごとに 1 行のテーブルが生成されます。 この操作を行わない場合は、"null bins" (タイム ビン値`StartTime`の`StopTime`行との間に対応する行がない`T`行) を追加します。 

多くの場合、これらのビンでテーブルを「埋め込む」ことが望まれます。これを行う 1 つの方法を次に示します。

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

上記のクエリの手順を次に示します。 

1. 演算子を`union`使用すると、テーブルに行を追加できます。 これらの行は、 に対する`union`式によって生成されます。
2. 演算子を`range`使用して、単一の行/列を持つテーブルを生成します。
   このテーブルは、作業のために以外の`mv-expand`目的では使用されません。
3. 演算子を`mv-expand`使用して、`range`と の間`StartTime`に 5 分のビンがある数の行`EndTime`を作成します。
4. のすべて`0`と`Count`.
5. 最後に、`summarize`この演算子を使用して、元の (左または外側) 引数からビン`union`をグループ化し、内部引数からビン (つまり、null ビン行) をグループ化します。 これにより、出力は、値がゼロまたは元のカウントのいずれかである、ビンごとに 1 行を持つことを確認します。  

## <a name="get-more-out-of-your-data-in-kusto-using-machine-learning"></a>機械学習を使用して Kusto でデータを活用する 

機械学習アルゴリズムを活用し、テレメトリ データから興味深い洞察を導き出す興味深いユース ケースは数多くあります。 多くの場合、これらのアルゴリズムは入力として非常に構造化されたデータセットを必要としますが、通常、生ログデータは必要な構造とサイズと一致しません。 

私たちの旅は、特定のBing推論サービスのエラー率の異常を探すから始まります。 Logs テーブルには 65B のレコードがあり、以下の単純なクエリは 250K エラーをフィルター処理し、異常検出関数[series_decompose_anomalies](series-decompose-anomaliesfunction.md)を利用するエラーカウントの時系列データを作成します。 異常は Kusto サービスによって検出され、時系列チャート上で赤い点として強調表示されます。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

サービスは、疑わしいエラー率を持ついくつかのタイムバケットを特定しました。 私はKustoを使用してこの時間枠を拡大し、トップエラーを探そうとしている'Message'列に集計するクエリを実行しています。 ページに収まるように、メッセージのスタック トレース全体から関連する部分をトリミングしました。 トップ8のエラーで成功を収めましたが、エラーメッセージが変更データを含むフォーマット文字列によって作成されたので、エラーの長い部分に達したことがわかります。 

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
|7125|メソッド 'ランサイクルから暫定データ' のメソッドが失敗しました。
|7125|推論ホストサービスの呼び出しに失敗しました。オブジェクト参照がオブジェクトのインスタンスに設定されていません。
|7124|予期しない推論システム エラーです。オブジェクト参照がオブジェクトのインスタンスに設定されていません。 
|5112|予期しない推論システム エラーです。オブジェクト参照がオブジェクトのインスタンスに設定されていません。
|174|推論ホストサービスの呼び出しが失敗しました。
|10|メソッド 'ランサイクルから暫定データ' のメソッドが失敗しました。
|10|推論システム エラー。マイクロソフトBing.プラットフォーム.推論.サービスマネージャー.ユーザー暫定データマネージャー例外:..
|3|推論ホストサービスの呼び出しが失敗しました。
|1|推論システムエラー.ソーシャルグラフ.BOSS.オペレーションレスポンス.AIS トレースId:8292FC561AC64BED8FA243808FE74EFD..
|1|推論システムエラー.ソーシャルグラフ.BOSS.オペレーションレスポンス.AIS トレースId: 5F79F7587FF943EC9B641E02E701AFBF..

これは、オペレータが`reduce`助けに来る場所です。 オペレーター`reduce`は、コード内の同じトレースインストルメンテーション ポイントによって発生した 63 の異なるエラーを特定し、そのタイム ウィンドウで意味のある追加のエラー トレースに集中するのに役立ちました。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| reduce by Message with threshold=0.35
| project Count, Pattern
```

|Count|パターン
|---|---
|7125|メソッド 'ランサイクルから暫定データ' のメソッドが失敗しました。
|  7125|推論ホストサービスの呼び出しに失敗しました。オブジェクト参照がオブジェクトのインスタンスに設定されていません。
|  7124|予期しない推論システム エラーです。オブジェクト参照がオブジェクトのインスタンスに設定されていません。
|  5112|予期しない推論システム エラーです。オブジェクト参照がオブジェクトのインスタンスに設定されていません。
|  174|推論ホストサービスの呼び出しが失敗しました。
|  63|推論システム エラー。マイクロソフトBing.プラットフォーム.推論。\*:\*オブジェクト BOSS に書き込む。\*: ソーシャルグラフ.BOSS.Reques..
|  10|メソッド 'ランサイクルから暫定データ' のメソッドが失敗しました。
|  10|推論システム エラー。マイクロソフトBing.プラットフォーム.推論.サービスマネージャー.ユーザー暫定データマネージャー例外:..
|  3|推論ホストサービスの呼び出しに失敗しました。サービスモデル。\*: オブジェクト、システム.サービスモデル.チャネル。\*+の\*は\*.. \* \*  シストで.

検出された異常に影響を及ぼした上位のエラーについてよく理解できたので、これらのエラーがシステム全体に及ぼす影響を理解したいと思います。 「ログ」テーブルには、「コンポーネント」や「クラスター」などの追加のディメンションデータが含まれています。新しい 'autocluster' プラグインは、単純なクエリでその洞察を導き出す手助けとなります。 次の例では、上位 4 つのエラーのそれぞれがコンポーネントに固有であり、上位 3 つのエラーは DB4 クラスターに固有ですが、4 つ目のエラーはすべてのクラスターで発生しています。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|Count |パーセント (%)|コンポーネント|クラスター|Message
|---|---|---|---|---
|7125|26.64|推論ホストサービス|DB4|メソッドのメソッド ..
|7125|26.64|不明なコンポーネント|DB4|推論ホストサービスの呼び出しに失敗しました。
|7124|26.64|推論アルゴリズムエキューター|DB4|予期しない推論システム エラーです。
|5112|19.11|推論アルゴリズムエキューター|*|予期しない推論システム エラーです。 

## <a name="mapping-values-from-one-set-to-another"></a>セット間の値のマッピング

一般的なユースケースは、より提示しやすい方法で結果を採用するのに役立つ値の静的マッピングを使用することです。  
たとえば、次のテーブルがあるとします。 DeviceModel は、デバイス名を参照する非常に便利な形式ではないデバイスのモデルを指定します。  


|DeviceModel |Count 
|---|---
|iPhone5,1 |32 
|iPhone3,2 |432 
|iPhone7,2 |55 
|iPhone5,2 |66 

  
より良い表現は次のようになります。  

|FriendlyName |Count 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

以下の 2 つのアプローチは、これを実現する方法を示しています。  

### <a name="mapping-using-dynamic-dictionary"></a>動的ディクショナリを使用したマッピング

以下の方法は、動的なディクショナリと動的アクセサーを使用してマッピングを実現する方法を示しています。

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



### <a name="mapping-using-static-table"></a>静的テーブルを使用したマッピング

以下の方法は、固定テーブルと結合演算子を使用してマッピングを実現する方法を示しています。
 
マッピング テーブルを作成します (1 回だけ)。

```kusto
.create table Devices (DeviceModel: string, FriendlyName: string) 

.ingest inline into table Devices 
    ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
```

デバイスのコンテンツ:

|DeviceModel |FriendlyName 
|---|---
|iPhone5,1 |iPhone 5 
|iPhone3,2 |iPhone 4 
|iPhone7,2 |iPhone 6 
|iPhone5,2 |iPhone5 


テストテーブルを作成する場合と同じトリック ソース:

```kusto
.create table Source (DeviceModel: string, Count: int)

.ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
```


参加とプロジェクト:

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


## <a name="creating-and-using-query-time-dimension-tables"></a>クエリ時間ディメンション テーブルの作成と使用

多くの場合、クエリの結果を、データベースに格納されていないアドホック ディメンション テーブルと結合する必要があります。 次のようにして、結果が単一のクエリにスコープ付けされたテーブルである式を定義できます。

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

## <a name="retrieving-the-latest-by-timestamp-records-per-identity"></a>ID ごとの最新 (タイムスタンプによる) レコードの取得

`id`列 (ユーザー ID やノード ID など、各行が関連付けられているエンティティを識別する) と`timestamp`列 (行の時間参照を提供する) と他の列を含むテーブルがあるとします。 目標は、列の値ごとに最新の 2 つのレコードを`id`返すクエリを`timestamp`作成することです。

これは[、上に入れ子になった演算子](topnestedoperator.md)を使用して行うことができます。
まずクエリを提供し、次に説明します。

```kusto
datatable(id:string, timestamp:datetime, bla:string)           // (1)
  [
  "Barak",  datetime(2015-01-01), "1",
  "Barak",  datetime(2016-01-01), "2",
  "Barak",  datetime(2017-01-20), "3",
  "Donald", datetime(2017-01-20), "4",
  "Donald", datetime(2017-01-18), "5",
  "Donald", datetime(2017-01-19), "6"
  ]
| top-nested   of id        by dummy0=max(1),                  // (2)
  top-nested 2 of timestamp by dummy1=max(timestamp),          // (3)
  top-nested   of bla       by dummy2=max(1)                   // (4)
| project-away dummy0, dummy1, dummy2                          // (5)
```

Notes
1. は`datatable`、デモンストレーション用のテストデータを作成する方法です。 実際には、もちろん、ここにデータがあります。
2. この行は基本的に""の全ての個別`id`値を返す" という意味です。
3. この行は、`timestamp`列を最大化する上位 2 件のレコードに対して、前のレベルの列`id`(ここでは just) と、このレベルで`timestamp`指定された列 (ここでは) を返します。
4. この行は、前のレベル`bla`で返された各レコードの列の値を追加します。 テーブルに他の対象の列がある場合は、その列ごとにこの行を繰り返します。
5. 最後に、[プロジェクトアウェイ演算子](projectawayoperator.md)を使用して、 によって導入された`top-nested`「余分な」列を削除します。

## <a name="extending-a-table-with-some-percent-of-total-calculation"></a>合計のパーセント計算でテーブルを拡張する

多くの場合、数値列を含む表形式の式がある場合、その列を合計に対するパーセンテージとして値と共にユーザーに提示できます。 たとえば、次のテーブルの値を持つクエリがあるとします。

|いくつかのシリーズ|サムイント|
|----------|-------|
|Foo       |    100|
|棒グラフ       |    200|

このテーブルを次のように表示します。

|いくつかのシリーズ|サムイント|Pct |
|----------|-------|----|
|Foo       |    100|33.3|
|棒グラフ       |    200|66.6|

これを行うには、`SomeInt`列の合計を計算し、この列の各値を合計で除算する必要があります。 as[演算子](asoperator.md)を使用して名前をこれらの結果に与えることによって、任意の結果に対してこれを行うことができます。

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

## <a name="performing-aggregations-over-a-sliding-window"></a>スライディング ウィンドウで集計を実行する
次の例は、スライディング ウィンドウを使用して列を集計する方法を示しています。 たとえば、タイムスタンプによる果物の価格を含む以下の表を取ることができます。 7日間のスライディングウィンドウを使用して、1日あたりの各果物の最小、最大、合計のコストを計算したいとします。 つまり、結果セット内の各レコードは過去 7 日間を集計し、結果には分析期間内の 1 日あたりのレコードが含まれます。  

フルーツテーブル: 

|Timestamp|Fruit|Price|
|---|---|---|
|2018-09-24 21:00:00.0000000|バナナ|3|
|2018-09-25 20:00:00.0000000|Apples|9|
|2018-09-26 03:00:00.0000000|バナナ|4|
|2018-09-27 10:00:00.0000000|梅|8|
|2018-09-28 07:00:00.0000000|バナナ|6|
|2018-09-29 21:00:00.0000000|バナナ|8|
|2018-09-30 01:00:00.0000000|梅|2|
|2018-10-01 05:00:00.0000000|バナナ|0|
|2018-10-02 02:00:00.0000000|バナナ|0|
|2018-10-03 13:00:00.0000000|梅|4|
|2018-10-04 14:00:00.0000000|Apples|8|
|2018-10-05 05:00:00.0000000|バナナ|2|
|2018-10-06 08:00:00.0000000|梅|8|
|2018-10-07 12:00:00.0000000|バナナ|0|

スライディング ウィンドウ集計クエリ (説明はクエリ結果の下に示します): 

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
|2018-10-01 00:00:00.0000000|Apples|9|9|9|
|2018-10-01 00:00:00.0000000|バナナ|0|8|18|
|2018-10-01 00:00:00.0000000|梅|2|8|10|
|2018-10-02 00:00:00.0000000|バナナ|0|8|18|
|2018-10-02 00:00:00.0000000|梅|2|8|10|
|2018-10-03 00:00:00.0000000|梅|2|8|14|
|2018-10-03 00:00:00.0000000|バナナ|0|8|14|
|2018-10-04 00:00:00.0000000|バナナ|0|8|14|
|2018-10-04 00:00:00.0000000|梅|2|4|6|
|2018-10-04 00:00:00.0000000|Apples|8|8|8|
|2018-10-05 00:00:00.0000000|バナナ|0|8|10|
|2018-10-05 00:00:00.0000000|梅|2|4|6|
|2018-10-05 00:00:00.0000000|Apples|8|8|8|
|2018-10-06 00:00:00.0000000|梅|2|8|14|
|2018-10-06 00:00:00.0000000|バナナ|0|2|2|
|2018-10-06 00:00:00.0000000|Apples|8|8|8|
|2018-10-07 00:00:00.0000000|バナナ|0|2|2|
|2018-10-07 00:00:00.0000000|梅|4|8|12|
|2018-10-07 00:00:00.0000000|Apples|8|8|8|

クエリの詳細: 

クエリは、入力テーブルの各レコードを 7 日間にわたって "ストレッチ" (重複) にして実際の表示をポストし、各レコードが実際に 7 回表示されます。 その結果、1日あたりの集計を実行する場合、集計には過去7日間のすべてのレコードが含まれる。

ステップバイステップの説明 (数字はクエリインラインコメントの数字を参照)。
1. 各レコードを 1d にビンします (_startを基準に)。 
2. レコードあたりの範囲の終点を決定します - _bin + 7d(開始 _、終了)_ 範囲外でない限り、この場合は調整されます。 
3. 各レコードに対して、現在のレコードの日付から 7 日 (タイムスタンプ) の配列を作成します。 
4. mv-配列を展開し、各レコードを互いに 1 日離れた 7 つのレコードに複製します。 
5. 日ごとに集計機能を実行します。 #4が原因で、これは実際には_過去_7日間を要約します。 
6. 最後に、最初の 7d のデータが不完全なため (最初の 7 日間は 7 日間のルックバック期間はありません)、最終結果から最初の 7 日間を除外します (2018-10-01 の集計にのみ参加します)。 

## <a name="find-preceding-event"></a>先行するイベントを検索する
次の例では、2 つのデータ セット間で先行するイベントを検索する方法を示します。  

*目的:* : 2 つのデータ・セットを指定すると、A と B は、B の各レコードについて、先行するイベントを A に見つけます (つまり、A のarg_maxレコードは B よりまだ「古い」です)。 たとえば、次のサンプル データ セットの出力を次に示します。 

```kusto
let A = datatable(Timestamp:datetime, Id:string, EventA:string)
[
    datetime(2019-01-01 00:00:00), "x", "Ax1",
    datetime(2019-01-01 00:00:01), "x", "Ax2",
    datetime(2019-01-01 00:00:02), "y", "Ay1",
    datetime(2019-01-01 00:00:05), "y", "Ay2",
    datetime(2019-01-01 00:00:00), "z", "Az1"
];
let B = datatable(Timestamp:datetime, Id:string, EventB:string)
[
    datetime(2019-01-01 00:00:03), "x", "B",
    datetime(2019-01-01 00:00:04), "x", "B",
    datetime(2019-01-01 00:00:04), "y", "B",
    datetime(2019-01-01 00:02:00), "z", "B"
];
A; B
```

|Timestamp|Id|イベントB|
|---|---|---|
|2019-01-01 00:00:00.0000000|x|アクス1|
|2019-01-01 00:00:00.0000000|z|Az1|
|2019-01-01 00:00:01.0000000|x|Ax2|
|2019-01-01 00:00:02.0000000|y|Ay1|
|2019-01-01 00:00:05.0000000|y|Ay2|

</br>

|Timestamp|Id|イベントア|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|B|
|2019-01-01 00:00:04.0000000|x|B|
|2019-01-01 00:00:04.0000000|y|B|
|2019-01-01 00:02:00.0000000|z|B|

予想される出力: 

|Id|Timestamp|イベントB|A_Timestamp|イベントア|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|y|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:02.0000000|Ay1|
|z|2019-01-01 00:02:00.0000000|B|2019-01-01 00:00:00.0000000|Az1|

この問題には、2 つの異なるアプローチが提案されています。 特定のデータ セットで両方をテストして、最適なデータ セットを見つける必要があります (異なるデータ セットで実行される可能性があります)。 

### <a name="suggestion-1"></a>提案#1:
この提案は、Id とタイムスタンプによって両方のデータ セットをシリアル化し、すべての B イベントを先行する`arg_max`A イベントとグループ化し、グループ内のすべての As から選択します。 

```kusto
A
| extend A_Timestamp = Timestamp, Kind="A"
| union (B | extend B_Timestamp = Timestamp, Kind="B")
| order by Id, Timestamp asc 
| extend t = iff(Kind == "A" and (prev(Kind) != "A" or prev(Id) != Id), 1, 0)
| extend t = row_cumsum(t)
| summarize Timestamp=make_list(Timestamp), EventB=make_list(EventB), arg_max(A_Timestamp, EventA) by t, Id
| mv-expand Timestamp to typeof(datetime), EventB to typeof(string)
| where isnotempty(EventB)
| project-away t
```

### <a name="suggestion-2"></a>提案#2:
この提案では、max-Lookback 期間 (A のレコードを B と比較できる "古い" の量) を定義し、Id とこのルックバック期間に 2 つのデータセットを結合する必要があります。 結合は、すべての候補 (B より古い、ルックバック期間内のすべての A レコード) を生成し、次に arg_min (TimestampB – TimestampA) で B に最も近いレコードをフィルター処理します。 ルックバック期間が短いほど、クエリのパフォーマンスが向上すると予想されます。 

次の例では、ルックバック期間が 1m に設定されているため、Id 'z' のレコードには対応する 'A' イベントがありません ('A' は 2m より古いため)。

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
) on Id, _range
| where isnull(A_Timestamp) or (A_Timestamp <= B_Timestamp and B_Timestamp <= A_Timestamp + _maxLookbackPeriod)
| extend diff = coalesce(B_Timestamp - A_Timestamp, _maxLookbackPeriod*2)
| summarize arg_min(diff, *) by ID
| project Id, B_Timestamp, A_Timestamp, EventB, EventA
```

|Id|B_Timestamp|A_Timestamp|イベントB|イベントア|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|y|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|B|Ay1|
|z|2019-01-01 00:02:00.0000000||B||