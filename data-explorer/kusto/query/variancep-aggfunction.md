---
title: variancep () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの variancep () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 70118746a079d76b1b6729bed3aae96c48399538
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338662"
---
# <a name="variancep-aggregation-function"></a>variancep () (集計関数)

グループ全体の*Expr*の分散を計算し、グループを[母集団](https://en.wikipedia.org/wiki/Statistical_population)と見なします。 

* 使用された式:

:::image type="content" source="images/variancep-aggfunction/variance-population.png" alt-text="分散の母集団":::

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>構文

集計の `variancep(` *Expr*`)`

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。 

## <a name="returns"></a>戻り値

グループ全体の*Expr*の分散値。
 
## <a name="examples"></a>例

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variancep(x) 
```

|list_x|variance_x|
|---|---|
|[1、2、3、4、5]|2|