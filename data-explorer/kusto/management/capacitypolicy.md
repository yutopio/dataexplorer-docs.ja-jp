---
title: 容量ポリシー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの容量ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: af648bd0a4b328477b14e20a2457e3e914df2827
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522000"
---
# <a name="capacity-policy"></a>容量ポリシー

容量ポリシーは、データインジェストやその他のデータグルーミング操作 (エクステントのマージなど) の実行に使用されるコンピューティング リソースを制御するために使用されます。

## <a name="the-capacity-policy-object"></a>能力ポリシー オブジェクト

容量ポリシーは`IngestionCapacity`、 、 `ExtentsMergeCapacity`、および`ExtentsPurgeRebuildCapacity``ExportCapacity`で構成されます。

### <a name="ingestion-capacity"></a>インジェスティキャパシティ

|プロパティ                           |Type    |説明                                                                                                                                                                               |
|-----------------------------------|--------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|クラスター最大同時実行操作 |long    |クラスター内の同時インジェスト操作の数の最大値                                                                                                            |
|コア使用率係数         |double  |取り込みキャパシティーを計算するときに使用するコアの割合の係数 (計算結果は常に) によって`ClusterMaximumConcurrentOperations`正規化されます。 |                                                                                                                             |

クラスターの合計取り込みキャパシティー [(.show capacity](../management/diagnostics.md#show-capacity)で示すように) は、次の方法で計算されます。

最小(`ClusterMaximumConcurrentOperations` `Number of nodes in cluster` , * 最大値`Core count per node` * `CoreUtilizationCoefficient`(1, )

> [!Note] 
> 3 つ以上のノードを持つクラスターでは、管理ノードはインジェスト操作の実行に参加`Number of nodes in cluster`しないため、1 ずつ削減されます。

### <a name="extents-merge-capacity"></a>エクステントマージ容量

|プロパティ                           |Type    |説明                                                                                    |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------------|
|最大同時操作数ノード |long    |単一ノードでの並行エクステントマージ/リビルド操作の最大数 |

クラスターの合計エクステントマージ容量[(.show capacity](../management/diagnostics.md#show-capacity)で示すように) は、次の方法で計算されます。

`Number of nodes in cluster`X`MaximumConcurrentOperationsPerNode`

> [!Note] 
> 3 つ以上のノードを持つクラスターでは、管理ノードはマージ操作の実行に関与`Number of nodes in cluster`しないため、1 ずつ削減されます。

### <a name="extents-purge-rebuild-capacity"></a>エクステントパージ再構築容量

|プロパティ                           |Type    |説明                                                                                                                           |
|-----------------------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------|
|最大同時操作数ノード |long    |単一ノードでの並行エクステントのパージ再構築操作 (パージ操作の再作成エクステント) の最大数 |

クラスターの合計エクステントのパージ再構築容量[(.show capacity](../management/diagnostics.md#show-capacity)で示すように) は、次の方法で計算されます。

`Number of nodes in cluster`X`MaximumConcurrentOperationsPerNode`

> [!Note] 
> 3 つ以上のノードを持つクラスターでは、管理ノードはマージ操作の実行に関与`Number of nodes in cluster`しないため、1 ずつ削減されます。

### <a name="export-capacity"></a>輸出能力

|プロパティ                           |Type    |説明                                                                                                                                                                            |
|-----------------------------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|クラスター最大同時実行操作 |long    |クラスター内の同時エクスポート操作の数の最大値。                                                                                                           |
|コア使用率係数         |double  |エクスポート容量を計算するときに使用するコアの割合の係数 (計算結果は常に`ClusterMaximumConcurrentOperations`によって正規化されます) |

クラスターのエクスポート容量の合計 ( [.show capacity](../management/diagnostics.md#show-capacity)で示す ) は、次の方法で計算されます。

最小(`ClusterMaximumConcurrentOperations` `Number of nodes in cluster` , * 最大値`Core count per node` * `CoreUtilizationCoefficient`(1, )

> [!Note] 
> 3 つ以上のノードを持つクラスターでは、管理ノードはエクスポート操作の実行に参加`Number of nodes in cluster`しないため、1 ずつ削減されます。

### <a name="extents-partition-capacity"></a>エクステント パーティション容量

|プロパティ                           |Type    |説明                                                                             |
|-----------------------------------|--------|----------------------------------------------------------------------------------------|
|クラスター最大同時実行操作 |long    |クラスター内の並行エクステント・パーティション操作の数の最大値。 |

クラスターの合計エクステント パーティション容量 ( [.show capacity](../management/diagnostics.md#show-capacity)で示す ) は、 `ClusterMaximumConcurrentOperations`1 つのプロパティで定義されます。

### <a name="defaults"></a>デフォルト

デフォルトの容量ポリシーは、次の JSON 表現を持っています。

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

> [!WARNING]
> Kustoチームと最初に相談することなく、容量ポリシーを変更することは**めったに**推奨されません。

## <a name="control-commands"></a>コントロール コマンド

* クラスターの現在の容量ポリシーを表示するには[、.show クラスター ポリシーの容量](capacity-policy.md#show-cluster-policy-capacity)を使用します。
* [クラスターの容量ポリシーを変更するには、.alter クラスター ポリシー](capacity-policy.md#alter-cluster-policy-capacity)容量を使用します。

## <a name="throttling"></a>Throttling

Kusto は、次のコマンドに対する同時要求の数を制限します。

1. 取り込み ([ここに](../management/data-ingestion/index.md)記載されているすべてのコマンドを含む)
      * 制限は[、容量ポリシー](#capacity-policy)で定義されているとおりです。
1. マージ
      * 制限は[、容量ポリシー](#capacity-policy)で定義されているとおりです。
1. パージ
      * グローバルは現在、クラスタごとに 1 つに固定されています。
      * パージ再構築キャパシティーは、パージ・コマンド中の並行再作成操作の数を決定するために内部的に使用されます (これにより、パージ・コマンドはブロック/スロットルされませんが、パージ・リビルド・キャパシティーに応じて高速/低速で動作します)。
1. Exports
      * 制限は[、容量ポリシー](#capacity-policy)で定義されているとおりです。


Kusto は、許可された同時操作を超えた操作を検出すると、429 HTTP コードで応答します。
クライアントは、バックオフ後に操作を再試行する必要があります。