---
title: 容量ポリシー制御コマンド-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの容量ポリシー制御コマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/02/2020
ms.openlocfilehash: 512ab14c2abc1f777376d81d4944caf2c3343513
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967334"
---
# <a name="capacity-policy-commands"></a>容量ポリシーのコマンド

次の制御コマンドを使用して、クラスターの[容量ポリシー](../management/capacitypolicy.md)を管理できます。

コマンドには[Alldatabasesadmin](../management/access-control/role-based-authorization.md)アクセス許可が必要です。

## <a name="show-cluster-policy-capacity"></a>クラスターポリシーの容量を表示する

```kusto
.show cluster policy capacity
```

クラスターの現在の容量ポリシーが表示されます。

**出力**

|ポリシー名 | エンティティ名 | ポリシー | 子エンティティ | エンティティ型
|---|---|---|---|---
|CapacityPolicy | | ポリシーを表す JSON 形式の文字列 | クラスター内のデータベースの一覧 |クラスター


## <a name="alter-cluster-policy-capacity"></a>クラスターポリシーの容量の変更

```kusto
.alter cluster policy capacity @'{ ... capacity policy JSON representation ... }'
.alter-merge cluster policy capacity @'{ ... capacity policy partial-JSON representation ... }'
```

**注**: クラスター容量ポリシーの変更が有効になるまでに最大1時間かかることがあります。

**例:**

* クラスターポリシーのすべてのプロパティを明示的に変更します。

```kusto
.alter cluster policy capacity
'{'
  '"IngestionCapacity": {'
    '"ClusterMaximumConcurrentOperations": 512,'
    '"CoreUtilizationCoefficient": 0.75'
  '},'
  '"ExtentsMergeCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 1'
  '},'
  '"ExtentsPurgeRebuildCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 1'
  '},'
  '"ExportCapacity": {'
    '"ClusterMaximumConcurrentOperations": 100,'
    '"CoreUtilizationCoefficient": 0.25'
  '},'
  '"ExtentsPartitionCapacity": {'
    '"ClusterMaximumConcurrentOperations": 4'
  '}'
'}'
```

* クラスターレベルポリシーの1つのプロパティを変更して、他のすべてのプロパティをそのまま維持します。

```kusto
.alter-merge cluster policy capacity
'{'
  '"ExtentsPartitionCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 4'
  '}'
'}'
```
