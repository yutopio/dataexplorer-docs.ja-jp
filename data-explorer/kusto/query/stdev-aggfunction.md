---
title: stdev () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの stdev () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3a29621a18a364145585022b1f0651100cadab1c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618541"
---
# <a name="stdev-aggregation-function"></a>stdev () (集計関数)

グループ全体の*Expr*の標準偏差を計算します。グループを[サンプル](https://en.wikipedia.org/wiki/Sample_%28statistics%29)として検討します。 

* 使用された式:

:::image type="content" source="images/stdev-aggfunction/stdev-sample.png" alt-text="Stdev サンプル":::

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

**構文**

集計`stdev(`の*Expr*`)`

**引数**

* *Expr*: 集計計算に使用される式です。 

**戻り値**

グループ全体の*Expr*の標準偏差値。
 
**使用例**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdev(x)

```

|list_x|stdev_x|
|---|---|
|[1、2、3、4、5]|1.58113883008419|