---
title: current_principal() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでcurrent_principal() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 36cebef7db3042bb59ccc5c7c25a56b2c1a661dc
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766038"
---
# <a name="current_principal"></a>current_principal()

::: zone pivot="azuredataexplorer"

クエリを実行している現在のプリンシパル名を返します。

**構文**

`current_principal()`

**戻り値**

現在のプリンシパルの完全修飾名 (FQN) を`string`.  
文字列は次のように形成されます。  
*プリンシプラタイプ*`=`*プリンシパルId*`;`*テナントId*

**例**

```kusto
print fqn=current_principal()
```

|fqn|
|---|
|aaduser=346e950e-4a62-42bf-96f5-4cf4eac3f11e;72f988bf-86f1-41af-91ab-2d7cd011db47|

::: zone-end

::: zone pivot="azuremonitor"

これは Azure モニターではサポートされていません。

::: zone-end
