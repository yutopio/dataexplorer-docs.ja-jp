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
ms.openlocfilehash: 6b0c1247c0d161caae3301dc674a701bd1019ad3
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91056936"
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

|プロパティ       |種類    |説明    |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |クラスター内の同時インジェスト操作数の最大値。               |
|CoreUtilizationCoefficient         |double  |インジェスト容量を計算するときに使用するコアの割合の係数。 計算の結果は常にによって正規化されます。 `ClusterMaximumConcurrentOperations` <br> で示されているように、クラスターのインジェスト容量の合計は次のように計算され [ます。容量を表示](../management/diagnostics.md#show-capacity)します。 <br> 最小値 ( `ClusterMaximumConcurrentOperations` , `Number of nodes in cluster` * 最大 (1, `Core count per node`  *  `CoreUtilizationCoefficient` ))

> [!Note]
> 3つ以上のノードがあるクラスターでは、管理ノードはインジェスト操作に関与しません。 `Number of nodes in cluster`が1つ減少します。

## <a name="extents-merge-capacity"></a>エクステントのマージ容量

|プロパティ                           |種類    |説明                                                                                                |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------------------------|
|MinimumConcurrentOperationsPerNode |long    |1つのノードでの同時実行エクステントのマージ/再構築操作の数の最小値。 既定値は1です。 |
|MaximumConcurrentOperationsPerNode |long    |1つのノードに対するマージ/再構築操作の同時実行数の最大値。 既定値は3です。 |

に示されているように、クラスターの合計エクステントのマージ容量 [。容量の表示](../management/diagnostics.md#show-capacity)は次のように計算されます。

`Number of nodes in cluster` 閉じる `Concurrent operations per node`

の有効値は、 `Concurrent operations per node` `MinimumConcurrentOperationsPerNode` `MaximumConcurrentOperationsPerNode` マージ操作の成功率が90% を超える限り、システムによって [,] の範囲で自動的に調整されます。

> [!Note]
> 3つ以上のノードがあるクラスターでは、管理ノードはマージ操作の実行に関与しません。 `Number of nodes in cluster`が1つ減少します。

## <a name="extents-purge-rebuild-capacity"></a>エクステント消去の再構築容量

|プロパティ                           |種類    |説明                                                                                                                           |
|-----------------------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------|
|MaximumConcurrentOperationsPerNode |long    |1つのノードでの消去操作の同時再構築エクステント数の最大値 |

クラスターの合計エクステントの削除の合計容量 (で示されているように、によって示さ [れます。容量の表示](../management/diagnostics.md#show-capacity)) は、次の方法で計算されます。

`Number of nodes in cluster` 閉じる `MaximumConcurrentOperationsPerNode`

> [!Note]
> 3つ以上のノードがあるクラスターでは、管理ノードはマージ操作の実行に関与しません。 `Number of nodes in cluster`が1つ減少します。

## <a name="export-capacity"></a>容量のエクスポート

|プロパティ                           |種類    |説明                                                                                                                                                                            |
|-----------------------------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |クラスター内の同時エクスポート操作数の最大値。                                           |
|CoreUtilizationCoefficient         |double  |エクスポート容量を計算するときに使用するコアの割合の係数。 計算の結果は、常にによって正規化され `ClusterMaximumConcurrentOperations` ます。 |

に示されているように、クラスターの合計エクスポート容量 [。容量の表示](../management/diagnostics.md#show-capacity)は次のように計算されます。

最小値 ( `ClusterMaximumConcurrentOperations` , `Number of nodes in cluster` * 最大 (1, `Core count per node`  *  `CoreUtilizationCoefficient` ))

> [!Note]
> 3つ以上のノードがあるクラスターでは、管理ノードはエクスポート操作に参加しません。 `Number of nodes in cluster`が1つ減少します。

## <a name="extents-partition-capacity"></a>エクステントパーティション容量

|プロパティ                           |種類    |説明                                                                                         |
|-----------------------------------|--------|----------------------------------------------------------------------------------------------------|
|ClusterMinimumConcurrentOperations |long    |クラスター内の同時エクステントのパーティション操作数の最小値。 既定値は1  |
|ClusterMaximumConcurrentOperations |long    |クラスター内の同時エクステントのパーティション操作数の最大値。 既定値:16 |

クラスターのエクステントの合計エクステント容量 (で示されているように)。 [容量を表示](../management/diagnostics.md#show-capacity)します。

の有効値は、 `Concurrent operations` `ClusterMinimumConcurrentOperations` `ClusterMaximumConcurrentOperations` パーティション分割操作の成功率が90% を超えている限り、システムによって [,] の範囲で自動的に調整されます。

## <a name="materialized-views-capacity-policy"></a>具体化ビューの容量ポリシー

[Alter cluster policy の容量](capacity-policy.md#alter-cluster-policy-capacity)を使用して容量ポリシーを変更します。 この変更には `AllDatabasesAdmin` アクセス許可が必要です。
このポリシーを使用すると、具体化されたビューの同時実行設定を変更できます。 この変更は、1つのクラスターで定義されている具体化されたビューが複数あり、クラスターがすべてのビューの具体化を維持できない場合に必要になることがあります。 既定では、具体化がクラスターのパフォーマンスに影響を与えないようにするために、同時実行の設定は比較的低くなっています。

> [!WARNING]
> 具体化されたビュー容量ポリシーは、クラスターのリソースが良好な場合 (CPU が少なく、使用可能なメモリが少ない場合) にのみ増やす必要があります。 リソースが制限されているときにこれらの値を大きくすると、リソースが枯渇し、クラスターのパフォーマンスが低下する可能性があります。

具体化されたビューの容量ポリシーは、クラスターの [容量ポリシー](#capacity-policy)の一部であり、次の JSON 表現を持ちます。

<!-- csl -->
``` 
{
   "MaterializedViewsCapacity": {
    "ClusterMaximumConcurrentOperations": 1,
    "ExtentsRebuildCapacity": {
      "ClusterMaximumConcurrentOperations": 50,
      "MaximumConcurrentOperationsPerNode": 5
    }
  }
}
```

### <a name="properties"></a>プロパティ

プロパティ | 説明
|---|---|
|`ClusterMaximumConcurrentOperations` | クラスターが同時に具体化できる具体化されたビューの最大数。 既定では、この値は1です。1つの個別のビューの具体化自体は、多数の同時操作を実行できます。 クラスターで定義されている具体化されたビューが複数あり、クラスターのリソースが良好な状態である場合は、この値を大きくすることをお勧めします。 |
| `ExtentsRebuildCapacity`|  具体化処理中に、具体化されたすべてのビューに対して実行される同時実行エクステントの再構築操作の数を決定します。 複数のビューが同時に実行されている場合は、 `ClusterMaximumConcurrentOperation` が1を超えるため、このプロパティによって定義されたクォータが共有されます。 同時実行エクステントの再構築操作の最大数がこの値を超えることはありません。 |

### <a name="extents-rebuild"></a>エクステントの再構築

エクステントの再構築操作の詳細については、「具体化された [ビューのしくみ](materialized-views/materialized-view-overview.md#how-materialized-views-work)」を参照してください。 再構築の最大エクステント数は、次の方法で計算されます。
    
```kusto
Maximum(`ClusterMaximumConcurrentOperations`, `Number of nodes in cluster` * `MaximumConcurrentOperationsPerNode`)
```

* 既定値は、合計50の同時実行の再構築とノードあたりの最大5です。
* この `ExtentsRebuildCapacity` ポリシーは上限としてのみ機能します。 実際に使用される値は、現在のクラスターの条件 (メモリ、CPU) と再構築操作に必要なリソース量の推定に基づいて、システムによって動的に決定されます。 実際には、同時実行は容量ポリシーで指定された値よりも大幅に低くなる可能性があります。
    * メトリックは、 `MaterializedViewExtentsRebuild` 各具体化サイクルで再構築されたエクステントの数に関する情報を提供します。 詳細については、「 [具体化ビューの監視](materialized-views/materialized-view-overview.md#materialized-views-monitoring)」を参照してください。

## <a name="defaults"></a>デフォルト

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

* を使用 [します。クラスターポリシーの容量を表示](capacity-policy.md#show-cluster-policy-capacity) して、クラスターの現在の容量ポリシーを表示します。

* クラスター [ポリシーの容量を変更](capacity-policy.md#alter-cluster-policy-capacity) して、クラスターの容量ポリシーを変更してください。

## <a name="throttling"></a>Throttling

Kusto は、次のユーザーによって開始されるコマンドの同時要求数を制限します。

* Ingestions ( [ここ](../../ingest-data-overview.md)に記載されているすべてのコマンドを含む)
   * 制限は、 [容量ポリシー](#capacity-policy)で定義されているとおりです。
* 破棄
   * 現在、グローバルはクラスターごとに1つずつ修正されています。
   * Purge rebuild capacity は、purge コマンドの実行中の同時再構築操作の数を決定するために、内部的に使用されます。 このプロセスにより、purge コマンドはブロックまたは調整されませんが、削除の再構築容量によっては、高速または低速になります。
* Exports
   * 制限は、 [容量ポリシー](#capacity-policy)で定義されているとおりです。

操作が許可されている同時実行操作を超えたことをクラスターが検出すると、429、"調整済み"、HTTP コードで応答します。
バックオフの後に操作を再試行してください。
