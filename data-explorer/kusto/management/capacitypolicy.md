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
ms.openlocfilehash: 21514de40910691e878dbc6d237d810a13676b40
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011535"
---
# <a name="capacity-policy"></a>キャパシティ ポリシー

容量ポリシーは、クラスターでのデータ管理操作に使用されるコンピューティングリソースを制御するために使用されます。

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
|ClusterMaximumConcurrentOperations |long    |クラスター内の同時インジェスト操作数の最大値                                                                                                            |
|CoreUtilizationCoefficient         |double  |インジェスト容量を計算するときに使用するコアの割合の係数 (計算の結果は常にによって正規化されます `ClusterMaximumConcurrentOperations` ) |                                                                                                                             |

クラスターのインジェスト容量の合計 (で示されているように、「[容量の表示](../management/diagnostics.md#show-capacity)」を参照) は次の方法で計算されます。

最小値 ( `ClusterMaximumConcurrentOperations` , `Number of nodes in cluster` * 最大 (1, `Core count per node`  *  `CoreUtilizationCoefficient` ))

> [!Note]
> 3つ以上のノードがあるクラスターでは、管理ノードがインジェスト操作の実行に参加していません。 `Number of nodes in cluster`が1つ減少します。

## <a name="extents-merge-capacity"></a>エクステントのマージ容量

|プロパティ                           |Type    |説明                                                                                    |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------------|
|MaximumConcurrentOperationsPerNode |long    |1つのノードでの同時実行エクステントのマージ/再構築操作数の最大値 |

クラスターの合計エクステントマージ容量 (で示されているように、「[容量の表示](../management/diagnostics.md#show-capacity)」を参照) は次の方法で計算されます。

`Number of nodes in cluster`閉じる`MaximumConcurrentOperationsPerNode`

> [!Note]
> * `MaximumConcurrentOperationsPerNode`値が大きい値に設定されていない限り、[1, 5] の範囲のシステムによって自動的に調整されます。
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
|ClusterMaximumConcurrentOperations |long    |クラスター内の同時エクスポート操作数の最大値。                                                                                                           |
|CoreUtilizationCoefficient         |double  |エクスポート容量を計算するときに使用するコアの割合の係数。 計算の結果は、常にによって正規化され `ClusterMaximumConcurrentOperations` ます。 |

クラスターの合計エクスポート容量 (で示されているように、「[容量の表示](../management/diagnostics.md#show-capacity)」を参照) は、次の方法で計算されます。

最小値 ( `ClusterMaximumConcurrentOperations` , `Number of nodes in cluster` * 最大 (1, `Core count per node`  *  `CoreUtilizationCoefficient` ))

> [!Note]
> 3つ以上のノードがあるクラスターでは、管理ノードがエクスポート操作の実行に参加していません。 `Number of nodes in cluster`が1つ減少します。

## <a name="extents-partition-capacity"></a>エクステントパーティション容量

|プロパティ                           |Type    |説明                                                                             |
|-----------------------------------|--------|----------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |クラスター内の同時エクステントのパーティション操作数の最大値。 |

クラスターのエクステントパーティションの合計容量 (で示さ[れ](../management/diagnostics.md#show-capacity)ているように) は、1つのプロパティで定義され `ClusterMaximumConcurrentOperations` ます。

> [!Note]
> `ClusterMaximumConcurrentOperations`値が大きい値に設定されていない限り、[1, 16] の範囲のシステムによって自動的に調整されます。

## <a name="defaults"></a>デフォルト

既定の容量ポリシーには、次の JSON 表現があります。

```kusto 
{
  "IngestionCapacity": {
    "ClusterMaximumConcurrentOperations": 512,
    "CoreUtilizationCoefficient": 0.75
  },
  "ExtentsMergeCapacity": {
    "MaximumConcurrentOperationsPerNode": 1
  },
  "ExtentsPurgeRebuildCapacity": {
    "MaximumConcurrentOperationsPerNode": 1
  },
  "ExportCapacity": {
    "ClusterMaximumConcurrentOperations": 100,
    "CoreUtilizationCoefficient": 0.25
  }
}
```

## <a name="control-commands"></a>管理コマンド

> [!WARNING]
> 容量ポリシーを変更する前に、Azure データエクスプローラーチームに問い合わせてください。

* を使用[します。クラスターポリシーの容量を表示](capacity-policy.md#show-cluster-policy-capacity)して、クラスターの現在の容量ポリシーを表示します。

* クラスター[ポリシーの容量を変更](capacity-policy.md#alter-cluster-policy-capacity)して、クラスターの容量ポリシーを変更してください。

## <a name="throttling"></a>Throttling

Kusto は、次のユーザーによって開始されるコマンドの同時要求数を制限します。

* Ingestions ([ここ](../../ingest-data-overview.md)に記載されているすべてのコマンドを含む)
   * 制限は、[容量ポリシー](#capacity-policy)で定義されているとおりです。
* 破棄
   * 現在、グローバルはクラスターごとに1つずつ修正されています。
   * Purge rebuild capacity は、purge コマンドの実行中の同時再構築操作の数を決定するために、内部的に使用されます。 このプロセスにより、purge コマンドはブロックまたは制限されませんが、削除の再構築容量によっては、処理速度が速くなるか遅くなります。
* Exports
   * 制限は、[容量ポリシー](#capacity-policy)で定義されているとおりです。

操作が許可されている同時実行操作を超えたことがクラスターによって検出されると、429 ("調整済み") HTTP コードを使用して応答します。
バックオフの後に操作を再試行してください。
