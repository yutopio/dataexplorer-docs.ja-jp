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
ms.openlocfilehash: 7ac5a92b2084eaf2b447f296be34b2f4e79e1bb7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520130"
---
# <a name="sandbox-policy"></a>サンドボックス ポリシー

次のコマンドを使用すると、Kusto エンジン サービスで[サンドボックス](../concepts/sandboxes.md)と[サンドボックス ポリシー](sandboxpolicy.md)を管理できます。

コマンドには[、すべてのデータベース管理者の](access-control/role-based-authorization.md)アクセス許可が必要です。

## <a name="sandbox-policy"></a>サンドボックス ポリシー

### <a name="show-cluster-policy-sandbox"></a>.show クラスター ポリシーサンドボックス

構成済みのすべてのサンドボックス ポリシーをクラスター レベルで表示します。

```kusto
.show cluster policy sandbox
```

### <a name="alter-cluster-policy-sandbox"></a>.alter クラスター ポリシーサンドボックス

クラスター レベルでのサンドボックス ポリシーのコレクションを変更します。

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

### <a name="drop-cluster-policy-sandbox"></a>.drop クラスター ポリシーサンドボックス

**すべての**サンドボックスポリシーを削除するには、次のコマンドを使用します。

```kusto
.delete cluster policy sandbox
```

