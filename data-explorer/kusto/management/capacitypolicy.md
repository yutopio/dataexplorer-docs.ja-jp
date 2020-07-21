---
title: 容量ポリシー-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの容量ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 3835cc3e50cc589e13f7d038a7c1f8f83def9d15
ms.sourcegitcommit: aacea5c4c397479e8254c1fe6ed0b2f333307b14
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86470079"
---
# <a name="capacity-policy"></a>キャパシティ ポリシー

容量ポリシーは、クラスターでのデータ管理操作のコンピューティングリソースを制御するために使用されます。

## <a name="the-capacity-policy-object"></a>容量ポリシーオブジェクト

容量ポリシーは次のもので構成されます。

* [IngestionCapacity](#ingestion-capacity)
* [ExtentsMergeCapacity](#extents-merge-capacity)
* [ExtentsPurgeRebuildCapacity](#extents-purge-rebuild-capacity)
* [ExportCapacity](#export-capacity)
* [ExtentsPartitionCapacity](#extents-partition-capacity)

## <a name="ingestion-capacity"></a>インジェスト容量

|プロパティ                           |Type    |説明                                                                                                                                                                               |
|-----------------------------------|--------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |クラスター内の同時インジェスト操作数の最大値                                          |
|CoreUtilizationCoefficient         |double  |インジェスト容量を計算するときに使用するコアの割合の係数。 計算の結果は常にによって正規化されます。`ClusterMaximumConcurrentOperations`                          |

で示されているように、クラスターのインジェスト容量の合計は次のように計算され[ます。容量を表示](../management/diagnostics.md#show-capacity)します。

最小値 ( `ClusterMaximumConcurrentOperations` , `Number of nodes in cluster` * 最大 (1, `Core count per node`  *  `CoreUtilizationCoefficient` ))

> [!Note]
> 3つ以上のノードがあるクラスターでは、管理ノードはインジェスト操作に関与しません。 `Number of nodes in cluster`が1つ減少します。

## <a name="extents-merge-capacity"></a>エクステントのマージ容量

|プロパティ                           |Type    |説明                                                                                                |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------------------------|
|MinimumConcurrentOperationsPerNode |long    |1つのノードでの同時実行エクステントのマージ/再構築操作の数の最小値。 既定値は1です。 |
|MaximumConcurrentOperationsPerNode |long    |1つのノードに対するマージ/再構築操作の同時実行数の最大値。 既定値は3です。 |

に示されているように、クラスターの合計エクステントのマージ容量[。容量の表示](../management/diagnostics.md#show-capacity)は次のように計算されます。

`Number of nodes in cluster`閉じる`Concurrent operations per node`

の有効値は、 `Concurrent operations per node` `MinimumConcurrentOperationsPerNode` `MaximumConcurrentOperationsPerNode` マージ操作の成功率が90% を超える限り、システムによって [,] の範囲で自動的に調整されます。

> [!Note]
> * 3つ以上のノードがあるクラスターでは、管理ノードはマージ操作の実行に関与しません。 `Number of nodes in cluster`が1つ減少します。

## <a name="extents-purge-rebuild-capacity"></a>エクステント消去の再構築容量

|プロパティ                           |Type    |説明                                                                                                                           |
|-----------------------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------|
|MaximumConcurrentOperationsPerNode |long    |1つのノードでの消去操作の同時再構築エクステント数の最大値 |

クラスターの合計エクステントの削除の合計容量 (で示されているように、によって示さ[れます。容量の表示](../management/diagnostics.md#show-capacity)) は、次の方法で計算されます。

`Number of nodes in cluster`閉じる`MaximumConcurrentOperationsPerNode`

> [!Note]
> 3つ以上のノードがあるクラスターでは、管理ノードはマージ操作の実行に関与しません。 `Number of nodes in cluster`が1つ減少します。

## <a name="export-capacity"></a>容量のエクスポート

|プロパティ                           |Type    |説明                                                                                                                                                                            |
|-----------------------------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |クラスター内の同時エクスポート操作数の最大値。                                           |
|CoreUtilizationCoefficient         |double  |エクスポート容量を計算するときに使用するコアの割合の係数。 計算の結果は、常にによって正規化され `ClusterMaximumConcurrentOperations` ます。 |

に示されているように、クラスターの合計エクスポート容量[。容量の表示](../management/diagnostics.md#show-capacity)は次のように計算されます。

最小値 ( `ClusterMaximumConcurrentOperations` , `Number of nodes in cluster` * 最大 (1, `Core count per node`  *  `CoreUtilizationCoefficient` ))

> [!Note]
> 3つ以上のノードがあるクラスターでは、管理ノードはエクスポート操作に参加しません。 `Number of nodes in cluster`が1つ減少します。

## <a name="extents-partition-capacity"></a>エクステントパーティション容量

|プロパティ                           |Type    |説明                                                                                         |
|-----------------------------------|--------|----------------------------------------------------------------------------------------------------|
|ClusterMinimumConcurrentOperations |long    |クラスター内の同時エクステントのパーティション操作数の最小値。 既定値は1  |
|ClusterMaximumConcurrentOperations |long    |クラスター内の同時エクステントのパーティション操作数の最大値。 既定値:16 |

クラスターのエクステントの合計エクステント容量 (で示されているように)。[容量を表示](../management/diagnostics.md#show-capacity)します。

の有効値は、 `Concurrent operations` `ClusterMinimumConcurrentOperations` `ClusterMaximumConcurrentOperations` パーティション分割操作の成功率が90% を超えている限り、システムによって [,] の範囲で自動的に調整されます。

## <a name="defaults"></a>[既定値]

既定の容量ポリシーには、次の JSON 表現があります。

```json
{
  "IngestionCapacity": {
    "ClusterMaximumConcurrentOperations": 512,
    "CoreUtilizationCoefficient": 0.75
  },
  "ExtentsMergeCapacity": {
    "MinimumConcurrentOperationsPerNode": 1,
    "MaximumConcurrentOperationsPerNode": 3
  },
  "ExtentsPurgeRebuildCapacity": {
    "MaximumConcurrentOperationsPerNode": 1
  },
  "ExportCapacity": {
    "ClusterMaximumConcurrentOperations": 100,
    "CoreUtilizationCoefficient": 0.25
  },
  "ExtentsPartitionCapacity": {
    "ClusterMinimumConcurrentOperations": 1,
    "ClusterMaximumConcurrentOperations": 16
  }
}
```

## <a name="control-commands"></a>管理コマンド

> [!WARNING]
> 容量ポリシーを変更する前に、Azure データエクスプローラーチームに問い合わせてください。

* を使用[します。クラスターポリシーの容量を表示](capacity-policy.md#show-cluster-policy-capacity)して、クラスターの現在の容量ポリシーを表示します。

* クラスター[ポリシーの容量を変更](capacity-policy.md#alter-cluster-policy-capacity)して、クラスターの容量ポリシーを変更してください。

## <a name="throttling"></a>調整

Kusto は、次のユーザーによって開始されるコマンドの同時要求数を制限します。

* Ingestions ([ここ](../../ingest-data-overview.md)に記載されているすべてのコマンドを含む)
   * 制限は、[容量ポリシー](#capacity-policy)で定義されているとおりです。
* 破棄
   * 現在、グローバルはクラスターごとに1つずつ修正されています。
   * Purge rebuild capacity は、purge コマンドの実行中の同時再構築操作の数を決定するために、内部的に使用されます。 このプロセスにより、purge コマンドはブロックまたは調整されませんが、削除の再構築容量によっては、高速または低速になります。
* Exports
   * 制限は、[容量ポリシー](#capacity-policy)で定義されているとおりです。

操作が許可されている同時実行操作を超えたことをクラスターが検出すると、429、"調整済み"、HTTP コードで応答します。
バックオフの後に操作を再試行してください。
