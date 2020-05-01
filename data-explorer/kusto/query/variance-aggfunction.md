---
title: variance () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの分散 () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9f8afae2413d65618cf6b6e2f400df2500b06078
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618049"
---
# <a name="variance-aggregation-function"></a>variance () (集計関数)

グループ全体の*Expr*の分散を計算し、グループを[サンプル](https://en.wikipedia.org/wiki/Sample_%28statistics%29)として検討します。 

* 使用された式:

:::image type="content" source="images/variance-aggfunction/variance-sample.png" alt-text="分散のサンプル":::

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

**構文**

集計`variance(`の*Expr*`)`

**引数**

* *Expr*: 集計計算に使用される式です。 

**戻り値**

グループ全体の*Expr*の分散値。
 
**使用例**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variance(x) 
```

|list_x|variance_x|
|---|---|
|[1、2、3、4、5]|2.5|