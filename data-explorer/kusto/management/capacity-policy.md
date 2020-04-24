---
title: 容量ポリシー制御コマンド - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの容量ポリシー制御コマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/02/2020
ms.openlocfilehash: 929cfa885a7373b400b832d908677a7a5fb93ef6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522085"
---
# <a name="capacity-policy-control-commands"></a>容量ポリシー制御コマンド

次の制御コマンドを使用して、クラスターの[容量ポリシー](../management/capacitypolicy.md)を管理できます。

コマンドには[、すべてのデータベース管理者の](../management/access-control/role-based-authorization.md)アクセス許可が必要です。

## <a name="show-cluster-policy-capacity"></a>クラスタ ポリシーの容量を表示する

```kusto
.show cluster policy capacity
```

クラスターの現在の容量ポリシーを表示します。

**出力**

|ポリシー名 | エンティティ名 | ポリシー | 子エンティティ | エンティティの種類
|---|---|---|---|---
|容量ポリシー | | ポリシーを表す JSON 形式の文字列 | クラスター内のデータベースの一覧 |クラスター


## <a name="alter-cluster-policy-capacity"></a>クラスタ ポリシーの容量を変更する

```kusto
.alter cluster policy capacity @'{ ... capacity policy JSON representation ... }'
.alter-merge cluster policy capacity @'{ ... capacity policy partial-JSON representation ... }'
```

**注**: クラスター容量ポリシーの変更が有効になるには、最大で 1 時間かかる場合があります。

**例:**

* クラスタ ポリシーのすべてのプロパティを明示的に変更する:

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

* クラスタ レベル ポリシーの単一のプロパティを変更し、その他のすべてのプロパティをそのまま維持します。

```kusto
.alter-merge cluster policy capacity
'{'
  '"ExtentsPartitionCapacity": {'
    '"MaximumConcurrentOperationsPerNode": 4'
  '}'
'}'
```
