---
title: stdevp() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで stdevp() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0549c15ec9e2435d242f210e6dfc2163796e5f39
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663199"
---
# <a name="stdevp-aggregation-function"></a>stdevp() (集計関数)

グループを[母集団](https://en.wikipedia.org/wiki/Statistical_population)と見なして、グループ全体の*Expr*の標準偏差を計算します。 

* 使用される式:

:::image type="content" source="images/aggregations/stdev-population.png" alt-text="スデフの人口":::

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`stdevp(`*エクスプの*要約`)`

**引数**

* *Expr*: 集計の計算に使用する式。 

**戻り値**

グループ全体の*Expr*の標準偏差値。
 
**使用例**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdevp(x)

```

|list_x|stdevp_x|
|---|---|
|[ 1, 2, 3, 4, 5]|1.4142135623731|