---
title: サンドボックスポリシー-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのサンドボックスポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 7d56d602f53db29f5ea558acb0e9e4288e5ac6e3
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967487"
---
# <a name="sandbox-policy-command"></a>サンドボックスポリシーコマンド

次のコマンドを使用すると、Kusto エンジンサービスの[サンド](../concepts/sandboxes.md)[ボックスポリシーおよびサンドボックスポリシー](sandboxpolicy.md)を管理できます。

コマンドには[Alldatabasesadmin](access-control/role-based-authorization.md)アクセス許可が必要です。

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

