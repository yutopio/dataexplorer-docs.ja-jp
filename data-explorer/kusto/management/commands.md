---
title: コマンド管理-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのコマンドの管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e6a31e2c79ae658cceecfdad0ce307e2522bb55b
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011416"
---
# <a name="commands-management"></a>コマンドの管理

## <a name="show-commands"></a>。コマンドを表示する 

`.show commands`最後の状態に達した管理コマンドを含むテーブルを返します。 これらのコマンドは、クエリで30日間使用できます。

Commands テーブルには、完了したすべてのコマンドのリソース消費の詳細を含む2つの列があります。

* TotalCpu-このコマンドで使用される CPU のクロック時間の合計 (ユーザーモード + カーネルモード)。
* ResourceUtilization 率-TotalCpu など、そのコマンドに関連するすべてのリソース使用情報が含まれます。

追跡されるリソース消費には、データの更新と、現在の管理コマンドに関連付けられているクエリが含まれます。
現在、コマンドテーブル (、、、、) では、一部の管理コマンドのみが対象と `.ingest` `.set` `.append` `.set-or-replace` `.set-or-append` なります。 コマンドテーブルに追加されるコマンドが徐々に増えています。

* データベース[管理者またはデータベースモニター](../management/access-control/role-based-authorization.md)は、データベースで呼び出された任意のコマンドを表示できます。
* 他のユーザーは、それらによって呼び出されたコマンドのみを表示できます。

**構文**

`.show` `commands`
 
**例**
 
|ClientActivityId |CommandType |テキスト |データベース |StartedOn |LastUpdatedOn |Duration |State |RootActivityId |User |FailureReason |アプリケーション |プリンシパル |TotalCpu |ResourceUtilization
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|KD2RunCommand;a069f9e3-6062-4a0e-aa82-75a1b5e16fb4 |ExtentsMerge   |。非同期操作をマージしています...    |DB1    |2017-09-05 11:08: 07.5738569    |2017-09-05 11:08: 09.1051161    |00:00: 01.5312592   |完了  |b965d809-3f3e-4f44-bd2b-5e1f49ac46c5   |AAD アプリ id = 5ba8cec2-9a70-e92c98cad651  |   |Kusto. Azure. .Svc |aadapp = 5ba8cec2 a70-e92c98cad651  |00:00: 03.5781250   |{"ScannedExtentsStatistics": {"MinDataScannedTime": null、"MaxDataScannedTime": null}、"CacheStatistics": {Memory ": {" ミス ": 2、" ヒット ":20}、" ディスク ": {" ミス ": 2、" ヒット ": 0}}、" MemoryPeak ": 159620640、" TotalCpu ":" 00:00: 03.5781250 "} 
|KE.RunCommand710e08ca-2cd3-4d2d-b7bd-2738d335aa50    |DataIngestPull |。 MyTableName に取り込む...   |TestDB |2017-09-04 16:00: 37.0915452    |2017-09-04 16:04: 37.2834555    |00:04: 00.1919103   |Failed |a8986e9e-81b0270d6fae4    |cooper@fabrikam.com    |ソケット接続は破棄されています。   |Kusto.Explorer |aaduser =...    |00:00:00   |{"ScannedExtentsStatistics": {"MinDataScannedTime": null、"MaxDataScannedTime": null}、"CacheStatistics": {"Memory": {"ミス": 0、ヒット ": 0}、" ディスク ": {" ミス ": 0、" ヒット ": 0}}、" MemoryPeak ": 0、" TotalCpu ":" 00:00:00 "} 
|KD2RunCommand;97db47e6-93e2-4306-8b7d-670f2c3307ff |ExtentsRebuild |。非同期操作をマージしています...    |DB2    |2017-09-18 13:29: 38.5945531    |2017-09-18 13:29: 39.9451163    |00:00: 01.3505632   |完了  |d5ebb755-d5df-4e94-b240-9accdf06c2d1   |AAD アプリ id = 5ba8cec2-9a70-e92c98cad651  |   |Kusto. Azure. .Svc |aadapp = 5ba8cec2 a70-e92c98cad651  |00:00: 00.8906250   |{"ScannedExtentsStatistics": {"MinDataScannedTime": null、"MaxDataScannedTime": null}、"CacheStatistics": {Memory ": {" ミス ": 0、" ヒット ": 1}、" ディスク ": {" ミス ": 0、" ヒット ": 0}}、" MemoryPeak ": 88828560、" TotalCpu ":" 00:00: 00.8906250 "} 

**例: ResourceUtilization 率列から特定のデータを抽出する**

ResourceUtilization 率列内のいずれかのプロパティにアクセスするには、ResourcesUtilization.xxx (xxx はプロパティ名) を呼び出します。
> [!NOTE] 
> `ResourceUtilization`は動的列です。 値を操作するには、最初にそれを特定のデータ型に変換する必要があります。 、、などの変換関数を使用し `tolong` `toint` `totimespan` ます。  

たとえば、オブジェクトに適用された

```kusto
.show commands
| where CommandType == "TableAppend"
| where StartedOn > ago(1h)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| top 3 by MemoryPeak desc
| project StartedOn, MemoryPeak, TotalCpu, Text
```

|StartedOn |MemoryPeak |TotalCpu |テキスト
|--|--|--|--
| 2017-09-28 12:11: 27.8155381   | 800396032 | 00:00: 04.5312500 |。追加 Server_Boots <\| Let bootStartsSourceTable = sessionstarts;...
| 2017-09-28 11:21: 26.7304547   | 750063056 | 00:00: 03.8218750 |. set-または-append WebUsage <\| データベース (' CuratedDB ')。WebUsage_v2 | 概要... | プロジェクト...
| 2017-09-28 12:16: 17.4762522   | 676289120 | 00:00: 00.0625000 |. set-または-append AtlasClusterEventStats with (...) <\| Atlas_Temp (datetime (2017-09-28 12:13: 28.7621737), datetime (2017-09-28 12:14: 28.8168492))

## <a name="investigating-performance-issues"></a>パフォーマンスの問題の調査

`.show`は、 `commands` 各コントロールコマンドによって使用されているリソースを表示するため、パフォーマンスの問題を調査するために使用できます。

次の例は、このような調査を行うための簡単で便利なクエリです。

### <a name="query-the-totalcpu-column"></a>TotalCpu 列に対してクエリを実行します。

過去1日の上位10個の CPU 消費クエリ。

```kusto
.show commands
| where StartedOn > ago(1d)
| top 10 by TotalCpu
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
```

TotalCpu が3分間経過した過去10時間のすべてのクエリ。

```kusto
.show commands
| where StartedOn > ago(10h) and TotalCpu > 3m
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
| order by TotalCpu 
```

過去24時間の TotalCpu が5分間に達したすべてのクエリは、ユーザーおよびプリンシパル別にグループ化されています。

```kusto
.show commands  
| where StartedOn > ago(24h)
| summarize TotalCount=count(), CountAboveThreshold=countif(TotalCpu > 2m), AverageCpu = avg(TotalCpu), MaxTotalCpu=max(TotalCpu), (50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu)=percentiles(TotalCpu, 50, 95) by User, Principal
| extend PercentageAboveThreshold = strcat(substring(CountAboveThreshold * 100 / TotalCount, 0, 5), "%")
| order by CountAboveThreshold desc
| project User, Principal, CountAboveThreshold, TotalCount, PercentageAboveThreshold, MaxTotalCpu, AverageCpu, 50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu
```

線上: 平均 CPU 対95パーパーセンタイルと最大 CPU。

```kusto
.show commands 
| where StartedOn > ago(1d) 
| summarize MaxCpu_Minutes=max(TotalCpu)/1m, 95th_Percentile_TotalCpu_Minutes=percentile(TotalCpu, 95)/1m, AverageCpu_Minutes=avg(TotalCpu)/1m by bin(StartedOn, 1m)
| render timechart
```

## <a name="query-the-memorypeak"></a>MemoryPeak のクエリを実行する

最上位の MemoryPeak 値を持つ最後の日の上位10件のクエリ。

```kusto
.show commands
| where StartedOn > ago(1d)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| project StartedOn, CommandType, ClientActivityId, TotalCpu, MemoryPeak
| top 10 by MemoryPeak  
```

平均 MemoryPeak 線上95パーパーセンタイルと Max MemoryPeak の比較。

```kusto
.show commands 
| where StartedOn > ago(1d)
| project MemoryPeak = tolong(ResourcesUtilization.MemoryPeak), StartedOn 
| summarize Max_MemoryPeak=max(MemoryPeak), 95th_Percentile_MemoryPeak=percentile(MemoryPeak, 95), Average_MemoryPeak=avg(MemoryPeak) by bin(StartedOn, 1m)
| render timechart
```
