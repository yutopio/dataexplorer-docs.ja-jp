---
title: current_principal ()-Azure データエクスプローラー |Microsoft Docs
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
ms.openlocfilehash: 0561ac200105015e6d1c1cce1c16fe5f60fc2ccf
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737710"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

クエリを実行している現在のプリンシパル名を返します。

**構文**

`current_principal()`

**戻り値**

としての現在のプリンシパル (完全修飾名) `string`。  
この文字列の形式は次のとおりです。  
*PrinciplaType*`=`*PrincipalId*PrincipalId`;`*TenantId*

**例**

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
