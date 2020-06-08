---
title: サンドボックスポリシー-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのサンドボックスポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 786f771878a7216b62dce127391f0e7967e954f6
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512455"
---
# <a name="sandbox-policy"></a>Sandbox ポリシー

Azure データエクスプローラーは、使用可能なリソースが制限され、セキュリティおよびリソースガバナンスのために制御される[サンド](../concepts/sandboxes.md)ボックス内で特定のプラグインを実行します。

サンドボックスは、Kusto エンジンのノードで実行されます。 制限事項のいくつかはサンドボックスポリシーで定義されています。サンドボックスの種類ごとに独自のポリシーを設定できます。

サンドボックスポリシーはクラスターレベルで管理され、クラスター内のすべてのノードに影響を与えます。

ポリシーを変更するには、 [Alldatabase admin](../management/access-control/role-based-authorization.md)のアクセス許可が必要です。

## <a name="the-policy-object"></a>ポリシーオブジェクト

サンドボックスポリシーには、次のプロパティがあります。

* **SandboxKind**: サンドボックスの種類を定義します (、 `PythonExecution` 、など `RExecution` )。
* **IsEnabled**: この種類のサンドボックスがクラスターのノードで実行できるかどうかを定義します。
* **TargetCountPerNode**: クラスターのノードで実行が許可されるこの種類のサンドボックスの数を定義します。
  * ノードあたりのプロセッサ数の 1 ~ 2 倍の値を指定できます。
  * 既定値は 16 です。
* **Maxcateateandbox**: 1 つのサンドボックスで使用できるすべての使用可能なコアに対する割合として、最大 CPU 速度を定義します。
  * 値は 1 ~ 100 の範囲で指定できます。
  * 既定値は 50 です。
* **MaxMemoryMbPerSandbox**: 1 つのサンドボックスで使用できるメモリの最大量 (メガバイト単位) を定義します。
  * 200 ~ 65536 (64 GB) の値を指定できます。
  * 既定値は 20480 (20GB) です。

## <a name="example"></a>例

次のポリシーは、とのサンドボックスにさまざまな制限を設定し `PythonExecution` `RExecution` ます。

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

> [!NOTE]
> * サンドボックスポリシーに対する変更は、変更が適用された時点から作成されたサンドボックスに適用されます。 ポリシーの変更前に事前に割り当てられたサンドボックスは、クエリの一部として使用されるまで、前のポリシーの制限に従って実行を継続します。
> * ポリシーの変更が有効になるまで、最大5分の遅延が発生する可能性があります。これは、クラスターノードがポリシーの変更を定期的にポーリングするためです。

## <a name="next-steps"></a>次のステップ

[サンドボックスポリシー制御コマンド](../management/sandbox-policy.md)を使用して、クラスターのサンドボックスポリシーを管理します。
 
