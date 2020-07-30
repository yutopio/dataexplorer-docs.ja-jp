---
title: minif () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの minif () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 91764aeb8c825a272c414df7a0572d3b8310e79f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346747"
---
# <a name="minif-aggregation-function"></a>minif () (集計関数)

*述語*がに評価されるグループ全体の最小値を返し `true` ます。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

「- [Min ()](min-aggfunction.md)関数」を参照してください。この関数は、述語式が指定されていないグループ全体の最小値を返します。

## <a name="syntax"></a>構文

`summarize``minif(` *Expr* `,` *述語*`)`

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。
* *述語*: true の場合、*式*の計算値が最小になるかどうかがチェックされます。

## <a name="returns"></a>戻り値

*述語*がに評価されるグループ全体の*Expr*の最小値 `true` 。

## <a name="examples"></a>例

```kusto
range x from 1 to 100 step 1
| summarize minif(x, x > 50)
```

|minif_x|
|---|
|51|