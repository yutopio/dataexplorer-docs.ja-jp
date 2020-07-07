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
ms.openlocfilehash: 33f21bdad11555ad2a55f285cbf40239236c561f
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967606"
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