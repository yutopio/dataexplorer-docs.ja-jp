---
title: stdev() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで stdev() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d54af583db6f7aca0b436040c453249207a5ae59
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663093"
---
# <a name="stdev-aggregation-function"></a>stdev() (集計関数)

グループを[標本](https://en.wikipedia.org/wiki/Sample_%28statistics%29)として考慮して、グループ全体の*Expr*の標準偏差を計算します。 

* 使用される式:

:::image type="content" source="images/aggregations/stdev-sample.png" alt-text="スデフサンプル":::

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`stdev(`*エクスプの*要約`)`

**引数**

* *Expr*: 集計の計算に使用する式。 

**戻り値**

グループ全体の*Expr*の標準偏差値。
 
**使用例**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdev(x)

```

|list_x|stdev_x|
|---|---|
|[ 1, 2, 3, 4, 5]|1.58113883008419|