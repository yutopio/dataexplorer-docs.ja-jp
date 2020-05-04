---
title: current_principal_is_member_of ()-Azure データエクスプローラー |Microsoft Docs
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
ms.openlocfilehash: 4b6f7d0b9ab4074f16ca00b4a3febb1a17351736
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737761"
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
*PrinciplaType*`=`*PrincipalId*PrincipalId`;`*TenantId*

| PrincipalType   | 文字のプレフィックス  |
|-----------------|-------------|
| AAD ユーザー        | `aaduser=`  |
| AAD グループ       | `aadgroup=` |
| AAD アプリケーション | `aadapp=`   |

**戻り値**

この関数では次の値が返されます。
* `true`: クエリを実行している現在のプリンシパルが、少なくとも1つの入力引数に対して正常に照合された場合。
* `false`: クエリを実行している現在のプリンシパルが、すべて`aadgroup=`の、 `aaduser=`いずれかのパラメーターのメンバーではなく、 `aadapp=`またはいずれの引数にも等しくない場合。
* `(null)`: クエリを実行している現在のプリンシパルが`aadgroup=` 、どの引数にも対応しておらず、 `aaduser=`または`aadapp=`いずれの引数にも等しくない場合、少なくとも1つのパラメーターが正しく解決されませんでした (AAD では事前に行われていません)。 

> [!NOTE]
> 関数は、3つの状態の値 (`true`、 `false`、および`null`) を返すため、成功したメンバーシップを確認するには、正の戻り値のみに依存することが重要です。 言い換えると、次の式は同じではありません。
> 
> * `where current_principal_is_member_of('non-existing-group')`
> * `where current_principal_is_member_of('non-existing-group') != false` 


**例**

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

Multple 引数ではなく動的配列を使用する場合:

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
