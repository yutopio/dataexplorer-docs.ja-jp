---
title: コマンド管理 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのコマンド管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5685833431529af22aa4d8778d121f5a4dec16d1
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744308"
---
# <a name="commands-management"></a>コマンド管理

## <a name="show-commands"></a>.show コマンド 

`.show``commands`コマンドは、最終状態に達した管理コマンドを含むテーブルを返します。 これらのコマンドは、30 日間のクエリに使用できます。

Commands テーブルには、完了した各コマンドのリソース消費の詳細を含む 2 つの列があります。
* 合計 CPU: このコマンドで消費される CPU クロック時間の合計 (ユーザー モード + カーネル モード)。
* リソース使用率: そのコマンドに関連するすべてのリソース使用率情報を含むオブジェクト (TotalCpu を含む)。

追跡されるリソースの消費量には、データの更新と、現在の管理コマンドに関連付けられているクエリが含まれます。
現在のところ、一部の管理コマンドのみがコマンド テーブル (.ingest、.set、.append、.set または replace、.set or-append) でカバーされており、徐々に、さらに多くのコマンドが今後追加されます。


* [データベース管理者またはデータベース・モニター](../management/access-control/role-based-authorization.md)は、データベースで呼び出されたコマンドを表示できます。
* 他のユーザーは、そのユーザーが呼び出したコマンドのみを表示できます。

**構文**

`.show` `commands`
 
**例**
 
|クライアントアクティビティId |CommandType |Text |データベース |スタートンオン |ラストアップデートオン |Duration |State |RootActivityId |User |FailureReason |Application |プリンシパル |合計Cpu |ResourceUtilization
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|KD2RunCommand;a069f9e3-6062-4a0e-aa82-75a1b5e16fb4 |エクステントマージ   |.マージ非同期操作.    |DB1    |2017-09-05 11:08:07.5738569    |2017-09-05 11:08:09.1051161    |00:00:01.5312592   |完了  |b965d809-3f3e-4f44-bd2b-5e1f49ac46c5   |AAD アプリ id=5ba8cec2-9a70-e92c98cad651  |   |クスト.Azure.DM.Svc |aadapp=5ba8cec2-9a70-e92c98cad651  |00:00:03.5781250   |{ "スキャンドエクステント統計": { "MinDataScannedtime": null、 "MaxDataScannedTime": null },"CacheStatistics": { メモリ" { "ミス": 2, "ヒット": "ディスク": { "ミス" : { "ミス" : 2, "ヒット" } } } } } } 
|柯。実行コマンド;710e08ca-2cd3-4d2d-b7bd-2738d335aa50 |データインジングエストプル |に取り込む .   |TestDB |2017-09-04 16:00:37.0915452    |2017-09-04 16:04:37.2834555    |00:04:00.1919103   |失敗 |a8986e9e-943f-81b0270d6fae4    |cooper@fabrikam.com    |ソケット接続は破棄されました。   |Kusto.Explorer |aaduser=..    |00:00:00   |{ "スキャンドエクステント統計" : { "MinDataScannedTime": null, "MaxDataScannedTime": null }, "キャッシュ統計" : { "メモリ" { "メモリ" { "ミス" : 0, ヒット" " "ディスク" : { "ミス" : "ヒット" : "ヒット" } 
|KD2RunCommand;97db47e6-93e2-4306-8b7d-670f2c3307ff |エクステント再構築 |.マージ非同期操作.    |DB2    |2017-09-18 13:29:38.5945531    |2017-09-18 13:29:39.9451163    |00:00:01.3505632   |完了  |d5ebb755-d5df-4e94-b240-9accdf06c2d1   |AAD アプリ id=5ba8cec2-9a70-e92c98cad651  |   |クスト.Azure.DM.Svc |aadapp=5ba8cec2-9a70-e92c98cad651  |00:00:00.8906250   |{ "スキャンドエクステント統計": { "MinDataScannedtime": null、 "MaxDataScannedTime": null }、"CacheStatistics": { "ミス": {ミス" " "ヒット" " "ヒット" " "ディスク" : {"ミス" " " "ミス" " " "ヒット" " } } } } } } } "MemoryPeak" : 88828560, "TotalCpu" " "00:00:00.8906250"} 

**例: ResourceUtilization 列から特定のデータを抽出する**

ResourceUtilization 列内のプロパティの 1 つにアクセスするには、ResourcesUtilization.xxx呼び出すことによって行われます (xxx はプロパティ名です)。
ResourceUtilization は動的な列であるため、値を処理するには、まず特定のデータ型に変換する必要があります (tolong、toint、totimespan、..  

次に例を示します。

```kusto
.show commands
| where CommandType == "TableAppend"
| where StartedOn > ago(1h)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| top 3 by MemoryPeak desc
| project StartedOn, MemoryPeak, TotalCpu, Text
```

|スタートンオン |メモリピーク |合計Cpu |Text
|--|--|--|--
| 2017-09-28 12:11:27.8155381   | 800396032 | 00:00:04.5312500  | .append Server_Boots \| <ブートスタートソーステーブル = セッション開始を可能にします。...
| 2017-09-28 11:21:26.7304547   | 750063056 | 00:00:03.8218750  | .設定または追加 WebUsage <\|データベース(「CuratedDB」)。WebUsage_v2 | 要約。。。 | プロジェクト。。。
| 2017-09-28 12:16:17.4762522   | 676289120 | 00:00:00.0625000  | .set-or-append AtlasClusterEventStats (.) \| <Atlas_Temp (2017-09-28 12:13:28.7621737),日時(2017-09-28 12:14:28.8168492))

## <a name="investigating-performance-issues"></a>パフォーマンスの問題の調査

`.show``commands`は、各 Control コマンドによって消費されるリソースを表示できるため、パフォーマンスの問題を調査するために使用できます。
次の例は、このような調査のための単純で便利なクエリです。

### <a name="querying-the-totalcpu-column"></a>合計 Cpu 列のクエリ

最終日の上位 10 CPU のクエリ:

```kusto
.show commands
| where StartedOn > ago(1d)
| top 10 by TotalCpu
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
```

TotalCpu が 3 分を超えた過去 10 時間のすべてのクエリ:

```kusto
.show commands
| where StartedOn > ago(10h) and TotalCpu > 3m
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
| order by TotalCpu 
```

過去 24 時間の TotalCpu が 5 分を超えていたすべてのクエリは、ユーザーとプリンシパル別にグループ化されています。

```kusto
.show commands  
| where StartedOn > ago(24h)
| summarize TotalCount=count(), CountAboveThreshold=countif(TotalCpu > 2m), AverageCpu = avg(TotalCpu), MaxTotalCpu=max(TotalCpu), (50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu)=percentiles(TotalCpu, 50, 95) by User, Principal
| extend PercentageAboveThreshold = strcat(substring(CountAboveThreshold * 100 / TotalCount, 0, 5), "%")
| order by CountAboveThreshold desc
| project User, Principal, CountAboveThreshold, TotalCount, PercentageAboveThreshold, MaxTotalCpu, AverageCpu, 50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu
```

タイムチャート: 平均 CPU 対 95 パーセンタイル対最大 CPU :

```kusto
.show commands 
| where StartedOn > ago(1d) 
| summarize MaxCpu_Minutes=max(TotalCpu)/1m, 95th_Percentile_TotalCpu_Minutes=percentile(TotalCpu, 95)/1m, AverageCpu_Minutes=avg(TotalCpu)/1m by bin(StartedOn, 1m)
| render timechart
```

## <a name="querying-the-memorypeak"></a>メモリピークのクエリ

最後の日の上位 10 件のクエリで、MemoryPeak の値が最も高い場合:

```kusto
.show commands
| where StartedOn > ago(1d)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| project StartedOn, CommandType, ClientActivityId, TotalCpu, MemoryPeak
| top 10 by MemoryPeak  
```

平均メモリピーク対95パーセンタイル対最大メモリピークのタイムチャート:

```kusto
.show commands 
| where StartedOn > ago(1d)
| project MemoryPeak = tolong(ResourcesUtilization.MemoryPeak), StartedOn 
| summarize Max_MemoryPeak=max(MemoryPeak), 95th_Percentile_MemoryPeak=percentile(MemoryPeak, 95), Average_MemoryPeak=avg(MemoryPeak) by bin(StartedOn, 1m)
| render timechart
```
