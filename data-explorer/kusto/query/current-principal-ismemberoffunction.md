---
title: current_principal_is_member_of ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの current_principal_is_member_of () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: fdd062abc02ff1d98ba935fc9016d0a1505e69c3
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550403"
---
# <a name="current_principal_is_member_of"></a>current_principal_is_member_of()

::: zone pivot="azuredataexplorer"

クエリを実行している現在のプリンシパルのグループメンバーシップまたはプリンシパル id を確認します。

```kusto
print current_principal_is_member_of(
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    )
```

**構文**

`current_principal_is_member_of`(`*list of string literals*`)

**引数**

* *式の一覧*-文字列リテラルのコンマ区切りのリスト。各リテラルは、次のように構成されたプリンシパル完全修飾名 (文字列形式) です。  
*PrinciplaType* `=`*PrincipalId* `;`*TenantId*

| PrincipalType   | 文字のプレフィックス  |
|-----------------|-------------|
| AAD ユーザー        | `aaduser=`  |
| AAD グループ       | `aadgroup=` |
| AAD アプリケーション | `aadapp=`   |

**戻り値**
  
この関数では次の値が返されます。
* `true`: クエリを実行している現在のプリンシパルが、少なくとも1つの入力引数に対して正常に照合された場合。
* `false`: クエリを実行している現在のプリンシパルが、すべての、いずれかのパラメーターのメンバーではなく、 `aadgroup=` またはいずれの引数にも等しくない場合 `aaduser=` `aadapp=` 。
* `(null)`: クエリを実行している現在のプリンシパルが、どの引数にも対応しておらず、またはいずれかの引数と等しくない場合に、少なくとも `aadgroup=` `aaduser=` `aadapp=` 1 つの小さい n 引数が正常に解決されなかった (Azure AD で押されていない)。 

> [!NOTE]
> 関数は、3つの状態の値 ( `true` 、 `false` 、および) を返すため `null` 、成功したメンバーシップを確認するには、正の戻り値のみに依存することが重要です。 言い換えると、次の式は同じではありません。
> 
> * `where current_principal_is_member_of('non-existing-group')`
> * `where current_principal_is_member_of('non-existing-group') != false` 


**例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print result=current_principal_is_member_of(
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    )
```

| 結果 |
|--------|
| (null) |

複数の引数ではなく動的配列を使用する場合:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print result=current_principal_is_member_of(
    dynamic([
    'aaduser=user1@fabrikam.com', 
    'aadgroup=group1@fabrikam.com',
    'aadapp=66ad1332-3a94-4a69-9fa2-17732f093664;72f988bf-86f1-41af-91ab-2d7cd011db47'
    ]))
```

| 結果 |
|--------|
| (null) |

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
