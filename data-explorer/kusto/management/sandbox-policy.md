---
title: サンドボックスポリシー-Azure データエクスプローラー |Microsoft Docs
description: Azure データエクスプローラーのサンドボックスポリシーコマンドについて説明します。 「サンドボックスポリシーを表示、調整、および削除する方法」を参照してください。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: ba37147db87c436aff7d77790641b17576e86392
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002969"
---
# <a name="sandbox-policy-command"></a>サンドボックスポリシーコマンド

次のコマンドを使用すると、Kusto エンジンサービスの[サンド](../concepts/sandboxes.md)[ボックスポリシーおよびサンドボックスポリシー](sandboxpolicy.md)を管理できます。

コマンドには [Alldatabasesadmin](access-control/role-based-authorization.md) アクセス許可が必要です。

## <a name="sandbox-policy"></a>Sandbox ポリシー

### <a name="show-cluster-policy-sandbox"></a>。クラスターポリシーサンドボックスを表示します

構成済みのすべてのサンドボックスポリシーがクラスターレベルで表示されます。

```kusto
.show cluster policy sandbox
```

### <a name="alter-cluster-policy-sandbox"></a>. alter cluster policy sandbox

クラスターレベルでサンドボックスポリシーのコレクションを変更します。

```kusto
.alter cluster policy sandbox @'['
  '{'
    '"SandboxKind": "PythonExecution",'
    '"IsEnabled": true,'
    '"TargetCountPerNode": 4,'
    '"MaxCpuRatePerSandbox": 50,'
    '"MaxMemoryMbPerSandbox": 10240'
  '},'
  '{'
    '"SandboxKind": "RExecution",'
    '"IsEnabled": true,'
    '"TargetCountPerNode": 4,'
    '"MaxCpuRatePerSandbox": 50,'
    '"MaxMemoryMbPerSandbox": 10240'
  '}'
']'
```

### <a name="drop-cluster-policy-sandbox"></a>。クラスターポリシーサンドボックスを削除します。

**すべて**のサンドボックスポリシーを削除するには、次のコマンドを使用します。

```kusto
.delete cluster policy sandbox
```
