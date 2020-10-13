---
title: Kusto RestrictedViewAccess ポリシー管理-Azure データエクスプローラー
description: Azure データエクスプローラーの RestrictedViewAccess policy コマンドについて説明します。 このポリシーを表示、有効化、無効化、変更、削除する方法を参照してください。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9ec328e3a15af3cedb833354f7e8ecea0fdc22c8
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002917"
---
# <a name="restricted_view_access-policy-command"></a>restricted_view_access ポリシーコマンド

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
