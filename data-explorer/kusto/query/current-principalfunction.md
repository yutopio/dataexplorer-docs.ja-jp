---
title: current_principal ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの current_principal () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b1d45fb8b0749a4be30854dd9b0120a7eb127bf2
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227300"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

クエリを実行している現在のプリンシパル名を返します。

**構文**

`current_principal()`

**戻り値**

としての現在のプリンシパル (完全修飾名) `string` 。  
この文字列の形式は次のとおりです。  
*PrinciplaType* `=`*PrincipalId* `;`*TenantId*

**例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print fqn=current_principal()
```

|fqn|
|---|
|aaduser = 346e950e-4a62-42bf-96f5-4cf4eac3f11e; 72f988bf-86f1-41af-91ab-2d7cd011db47|

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
