---
title: current_principal_is_member_of() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでcurrent_principal_is_member_of() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 03d565c9eb61703b326a01b31bb6b1a79742f006
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766057"
---
# <a name="current_principal_is_member_of"></a>current_principal_is_member_of()

::: zone pivot="azuredataexplorer"

クエリを実行している現在のプリンシパルのグループ メンバーシップまたはプリンシパル ID を確認します。

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

* *式のリスト*- 各リテラルが、次のように形成された主体完全修飾名 (FQN) 文字列である文字列リテラルのコンマ区切りのリスト。  
*プリンシプラタイプ*`=`*プリンシパルId*`;`*テナントId*

| プリンシパルタイプ   | FQN プレフィックス  |
|-----------------|-------------|
| AAD ユーザー        | `aaduser=`  |
| AADグループ       | `aadgroup=` |
| AAD アプリケーション | `aadapp=`   |

**戻り値**

この関数では次の値が返されます。
* `true`: クエリを実行している現在のプリンシパルが、少なくとも 1 つの入力引数に対して正常に一致した場合。
* `false`: クエリを実行している現在のプリンシパルが`aadgroup=`FQN 引数のメンバーではなく、どの引数`aaduser=`または`aadapp=`FQN 引数にも等しくない場合。
* `(null)`: クエリを実行している現在のプリンシパルが`aadgroup=`FQN 引数のメンバーでなく、どの引数`aaduser=`または`aadapp=`FQN 引数にも等しくなく、少なくとも 1 つの FQN 引数が正常に解決されなかった場合 (AAD ではプリセされていません)。 

> [!NOTE]
> 関数は 3 状態の値 (`true` `false`、 `null`、および ) を返すため、正の戻り値だけに依存してメンバーシップが成功したことを確認することが重要です。 つまり、次の式は同じではありません。
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

マルチプル引数の代わりに動的配列を使用する:

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

これは Azure モニターではサポートされていません。

::: zone-end
