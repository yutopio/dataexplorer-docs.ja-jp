---
title: Kusto RestrictedViewAccess ポリシー管理-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの RestrictedViewAccess ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9da59a53819396cf2cbd522f4a1e1296f585bf2f
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617549"
---
# <a name="restrictedviewaccess-policy"></a>RestrictedViewAccess ポリシー

*RestrictedViewAccess*ポリシーについては、[こちら](../management/restrictedviewaccesspolicy.md)を参照してください。

データベース内のテーブルでポリシーを有効または無効にする制御コマンドは次のとおりです。

ポリシーを有効または無効にするには:
```kusto
.alter table TableName policy restricted_view_access true|false
```

複数のテーブルのポリシーを有効または無効にするには、次のようにします。
```kusto
.alter tables (TableName1, ..., TableNameN) policy restricted_view_access true|false
```

ポリシーを表示するには:
```kusto
.show table TableName policy restricted_view_access  

.show table * policy restricted_view_access  
```

ポリシーを削除するには (無効にするのと同じ):
```kusto
.delete table TableName policy restricted_view_access  
```