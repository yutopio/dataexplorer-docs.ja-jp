---
title: stdevp () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの stdevp () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c5dffc8695df466dfc1ac9f0c5bcc4a40f687b2a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342718"
---
# <a name="stdevp-aggregation-function"></a>stdevp () (集計関数)

グループ全体の*Expr*の標準偏差を計算し、グループを[母集団](https://en.wikipedia.org/wiki/Statistical_population)と見なします。 

* 使用された式:

:::image type="content" source="images/stdevp-aggfunction/stdev-population.png" alt-text="Stdev の母集団":::

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>構文

集計の `stdevp(` *Expr*`)`

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。 

## <a name="returns"></a>戻り値

グループ全体の*Expr*の標準偏差値。
 
## <a name="examples"></a>例

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), stdevp(x)

```

|list_x|stdevp_x|
|---|---|
|[1、2、3、4、5]|1.4142135623731|