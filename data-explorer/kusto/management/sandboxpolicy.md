---
title: サンドボックス ポリシー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのサンドボックス ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 0a59e25c6c38d3189330299af1b19f89357cb456
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520062"
---
# <a name="sandbox-policy"></a>サンドボックス ポリシー

## <a name="overview"></a>概要

Kusto は[サンドボックス](../concepts/sandboxes.md)内で特定のプラグインを実行することをサポートしており、サンドボックスで利用可能なリソースは、セキュリティ上の目的で、リソースガバナンスの目的で制限され、制御されます。

サンドボックスは Kusto エンジン サービスのノードで実行され、サンドボックス の種類ごとに独自のサンドボックス ポリシーを持つことができるサンドボックス ポリシーで制限の一部が定義されます。

サンドボックス ポリシーはクラスター レベルで管理され、クラスター内のすべてのノードに影響します。

ポリシーを変更するには[、AllDatabasesAdmin アクセス許可が必要です](../management/access-control/role-based-authorization.md)。

## <a name="the-policy-object"></a>ポリシー オブジェクト

サンドボックス ポリシーには、次のプロパティがあります。

* **サンドボックスの種類**: サンドボックスの種類を定義`PythonExecution``RExecution`します ( など ) 。
* **IsEnabled**: この種のサンドボックスがクラスタのノードで実行できるかどうかを定義します。
* **TargetCountPerNode**: この種のサンドボックスの実行をクラスターのノードで許可する数を定義します。
  * 値は、ノードあたりのプロセッサ数の 1 から 2 倍の値にすることができます。
  * 既定値は `16` です。
* **MaxCpuRatePerサンドボックス**: 1 つのサンドボックスが使用できるすべての使用可能なコアの割合で最大 CPU レートを定義します。
  * 値は 1 ~ 100 の間で指定できます。
  * 既定値は `50` です。
* **1**つのサンドボックスで使用できるメモリの最大量 (MB 単位) を定義します。
  * 値は 200 ~ 65536 (64 GB) の間にすることができます。
  * デフォルト値は`20480`(20GB)です。

## <a name="example"></a>例

次のポリシーでは、2 種類のサンドボックスに異なる制限を`PythonExecution`設定`RExecution`します。

```json
[
  {
    "SandboxKind": "PythonExecution",
    "IsEnabled": true,
    "TargetCountPerNode": 4,
    "MaxCpuRatePerSandbox": 55,
    "MaxMemoryMbPerSandbox": 65536
  },
  {
    "SandboxKind": "RExecution",
    "IsEnabled": true,
    "TargetCountPerNode": 2,
    "MaxCpuRatePerSandbox": 50,
    "MaxMemoryMbPerSandbox": 10240
  }
]
```

## <a name="notes"></a>Notes

* サンドボックス ポリシーの変更は、変更が適用された時点から作成されたサンボックスに適用されます。
  * ポリシー変更前に事前に割り当てられたサンドボックスは、クエリの一部として使用されるまで、以前のポリシー制限に従って実行を継続します。
* クラスター ノードが定期的にポリシーの変更をポーリングするため、ポリシーの変更が有効になるまで最大 5 分の遅延が発生する可能性があります。

## <a name="next-steps"></a>次のステップ

サンドボックス[ポリシーコントロール コマンド](../management/sandbox-policy.md)を使用して、クラスターのサンドボックス ポリシーを管理します。