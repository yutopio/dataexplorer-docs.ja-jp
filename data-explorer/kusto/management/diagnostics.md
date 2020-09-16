---
title: 診断情報-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの診断情報について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 60d25403b230be9ef625a6d52d1fdb159f9fc4e3
ms.sourcegitcommit: 313a91d2a34383b5a6e39add6c8b7fabb4f8d39a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90680662"
---
# <a name="diagnostic-information"></a>診断情報

これらのコマンドを使用して、システム診断情報を表示できます。

* [。クラスターを表示します](#show-cluster)
* [。診断を表示します](#show-diagnostics)
* [。容量を表示します。](#show-capacity)
* [。操作を表示します。](#show-operations)

## <a name="show-cluster"></a>。クラスターを表示します

```kusto
.show cluster
```

クラスター内で現在アクティブになっているノードごとに1つのレコードを含むセットを返します。  

**結果** 

|出力列 |種類 |[説明]
|---|---|---|
|NodeId|String|ノードを識別します。 クラスターが Azure にデプロイされている場合、ノード ID はノードの Azure RoleId になります。
|Address|String |ノード間通信のためにクラスターによって使用される内部エンドポイント
|名前 |String |ノードの内部名。 名前には、コンピューター名、プロセス名、およびプロセス ID が含まれます。
|StartTime |DateTime |ノードで現在の Kusto インスタンス化が開始された日付/時刻 (UTC)。 この値は、ノード (またはノード上で実行されている Kusto) が最近再起動されたかどうかを検出するために使用できます。
|IsAdmin |ブール型 |このノードが現在クラスターの "リーダー" である場合 
|MachineTotalMemory  |Int64 |ノードの RAM の量。
|MachineAvailableMemory  |Int64 |現在ノードで使用可能な RAM の量。
|ProcessorCount  |Int32 |ノード上のプロセッサの数。
|環境の説明  |string |JSON としてシリアル化されたノードの環境に関する追加情報。 たとえば、[アップグレード/障害ドメイン] として

**例**

```kusto
.show cluster
```

NodeID|Address|名前|StartTime|IsAdmin|MachineTotalMemory|MachineAvailableMemory|ProcessorCount|環境の説明
---|---|---|---|---|---|---|---|---
Kusto. Azure. Svc_IN_1|net.tcp://100.112.150.30: 23107/|Kusto. Azure. Svc_IN_4/RD000D3AB1E9BD/WaWorkerHost/3820|2016-01-15 02:00: 22.6522152|○|274877435904|247797796864|16|{"UpdateDomain": 0、"FaultDomain": 0}
Kusto. Azure. Svc_IN_3|net.tcp://100.112.154.34: 23107/|Kusto. Azure. Svc_IN_3/RD000D3AB1E062/WaWorkerHost/2760|2016-01-15 05:52: 52.1434683|×|274877435904|258740346880|16|{"UpdateDomain": 1, "FaultDomain": 1}
Kusto. Azure. Svc_IN_2|net.tcp://100.112.128.40: 23107/|Kusto. Azure. Svc_IN_2/rd000d3ab1e052015 年4月3日の waworkerhost376|2016-01-15 07:17: 18.0699790|×|274877435904|244232339456|16|{"UpdateDomain": 2, "FaultDomain": 2}
Kusto. Azure. Svc_IN_0|net.tcp://100.112.138.15: 23107/|Kusto. Azure. Svc_IN_0/RD000D3AB0D6C6/WaWorkerHost/3208|2016-01-15 09:46: 36.9865016|×|274877435904|238414581760|16|{"UpdateDomain": 3、"FaultDomain": 3}


## <a name="show-diagnostics"></a>。診断を表示します

```kusto
.show diagnostics
```

Kusto クラスターの正常性状態に関する情報を返します。
 
**戻り値**

|出力パラメーター |種類 |[説明]|
|-----------------|-----|-----------| 
|IsHealthy|ブール型|クラスターが正常であるかどうか
|IsScaleOutRequired|ブール型|コンピューティングノードを追加してクラスターのサイズを増やす必要がある場合
|MachinesTotal|Int64|クラスター内のマシンの数
|オフラインのスケジュール|Int64|現在オフラインになっているマシンの数
|NodeLastRestartedOn|DateTime|クラスター内のノードが再起動された最後の日付/時刻
|AdminLastElectedOn|DateTime|クラスター管理者ロールの最後の日付/時刻の所有権が変更されました
|MemoryLoadFactor|Double|クラスターによって保持されているデータの量 (最大容量は 100.0)
|ExtentsTotal|Int64|すべてのデータベースとすべてのテーブルにわたる、現在のクラスターのデータエクステントの合計数
|予約済み|Int64|
|予約済み|Int64|
|InstancesTargetBasedOnDataCapacity|Int64|ClusterDataCapacityFactor を80未満にするために必要なインスタンスの数。 この値は、すべてのマシンのサイズが同じ場合にのみ有効です
|TotalOriginalDataSize|Int64|最初の取り込まれたデータの合計サイズ (バイト単位)
|TotalExtentSize|Int64|圧縮後にバイト単位でインデックスを作成した後の、格納されたデータの合計サイズ
|IngestionsLoadFactor|Double|使用されたクラスターインジェスト容量の割合。 最大容量は、コマンドを使用して表示できます。 `.show capacity`
|IngestionsInProgress|Int64|現在実行されているインジェスト操作の数
|IngestionsSuccessRate|Double|過去10分間に正常に完了したインジェスト操作の割合
|MergesInProgress|Int64|現在実行されているエクステントのマージ操作の数
|BuildVersion|String|クラスターに展開されている Kusto ソフトウェアのバージョン
|BuildTime|DateTime|Kusto ソフトウェアのビルドバージョンの日付/時刻。
|ClusterDataCapacityFactor|Double|使用されているクラスターデータ容量の割合。 割合は、SUM (エクステントサイズデータ)/合計 (SSD キャッシュサイズ) として計算されます。
|IsDataWarmingRequired|ブール型|内部: クラスターのウォームクエリを実行し、データをローカル SSD キャッシュに取り込む必要がある場合 
|DataWarmingLastRunOn|DateTime|ウォームデータがクラスターで実行された最後の日付/時刻
|MergesSuccessRate|Double|過去10分間に正常に完了したマージ操作の割合。
|NotHealthyReason|String|クラスターが正常でない理由を指定します 
|IsAttentionRequired|ブール型|クラスターに操作チームの注意が必要な場合
|AttentionRequiredReason|String|クラスターの注意が必要な理由を指定します
|ProductVersion|String|製品情報 (分岐、バージョンなど) を指定します
|FailedIngestOperations|Int64|過去10分間に失敗したインジェスト操作の数
|失敗した Mergeoperation|Int64|前の1時間に失敗したマージ操作の数
|MaxExtentsInSingleTable|Int64|テーブル内のエクステントの最大数 (TableWithMaxExtents)
|TableWithMaxExtents|String|エクステントの最大数を含むテーブル (MaxExtentsInSingleTable)
|WarmExtentSize|Double|ホットキャッシュ内のエクステントの合計サイズ (バイト単位)
|NumberOfDatabases|Int32|クラスター内のデータベースの数

## <a name="show-capacity"></a>。容量を表示します。

```kusto
.show capacity
```

各リソースの推定クラスター容量の計算結果を返します。
 
**結果**

|出力パラメーター |種類 |説明 
|---|---|---
|リソース |String |リソースの名前 
|合計 |Int64 |使用可能なリソースの総量 (種類 ' Resource ')。 たとえば、同時実行 ingestions の数
|使用量 |Int64 |現在、' Resource ' 型のリソースの消費量が使用されています
|残り |Int64 |種類 ' Resource ' の残りのリソースの量
 
**例**

|リソース |合計 |使用量 |残り
|---|---|---|---
|ingestions |576 |1 |575

## <a name="show-operations"></a>。操作を表示します。

このコマンドは、新しい管理ノードが選択されてからのすべての管理操作を含むテーブルを返します。

|構文オプション |説明|
|---|---| 
|`.show` `operations`              |クラスターが処理しているか処理されているすべての操作を返します
|`.show``operations` *OperationId*|特定の ID の操作の状態を返します。
|`.show``operations` `(` *OperationId1* `,` *OperationId2* `,` ...)|特定の Id の操作の状態を返します。

**結果**
 
|出力パラメーター |種類 |説明
|---|---|---
|id |String |操作 id
|操作 |String |管理者コマンドのエイリアス
|NodeId |String |コマンドがリモートで実行されている場合 (DataIngestPull など)。 ノード ID には、を実行しているリモートノードの ID が含まれます。
|StartedOn |DateTime |操作が開始された日付/時刻 (UTC) 
|LastUpdatedOn |DateTime |操作が最後に更新された日付/時刻 (UTC)。 操作は、操作内のステップでも、完了ステップでもかまいません。
|Duration |DateTime |LastUpdateOn と StartedOn の間の期間
|State |String |値が "処理中"、"完了"、または "失敗" のコマンド状態
|Status |String |失敗した操作のエラーを含む追加のヘルプ文字列
 
**例**
 
|id |操作 |ノード ID |開始日 |最終更新日 |Duration |State |Status 
|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |SchemaShow | |2015-01-06 08:47: 01.0000000 |2015-01-06 08:47: 01.0000000 |0001-01-01 00:00: 00.0000000 |完了 | 
|84 1fafa4076a47 cba93008-4836da9c75 |DataIngestPull |Kusto. Azure. Svc_IN_1 |2015-01-06 08:47: 02.0000000 |2015-01-06 08:48: 19.0000000 |0001-01-01 00:01: 17.0000000 |完了 | 
|e198c519-5263-4629-a158-8d68f7a1022f |表示の表示 | |2015-01-06 08:47: 18.0000000 |2015-01-06 08:47: 18.0000000 |0001-01-01 00:00: 00.0000000 |完了 |
|a9f287a1-f3e6-4154-ad18-b86438da0929 |ExtentsDrop | |2015-01-11 08:41: 01.0000000 |0001-01-01 00:00: 00.0000000 |0001-01-01 00:00: 00.0000000 |InProgress |
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DataIngestPull | |2015-01-10 14:57: 41.0000000 |2015-01-10 14:57: 41.0000000 |0001-01-01 00:00: 00.0000000 |失敗 |コレクションが変更されました。 列挙操作は実行されない可能性があります |
