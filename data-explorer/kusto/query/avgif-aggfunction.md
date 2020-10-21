---
title: avgif () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの avgif () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e0d112554ff77cb9c591f787bbb62f9a92e3b19d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248026"
---
# <a name="avgif-aggregation-function"></a>avgif () (集計関数)

*述語*がに評価されるグループ全体の*Expr*の[平均](avg-aggfunction.md)を計算し `true` ます。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>構文

`avgif(` *Expr* `, ` *述語*の集計`)`

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。 値を持つレコード `null` は無視され、計算には含まれません。
* *述語*: true の場合、 *式* の計算値が平均に加算されます。

## <a name="returns"></a>戻り値

*述語*がに評価されるグループ全体の*Expr*の平均値 `true` 。
 
## <a name="examples"></a>例

```kusto
range x from 1 to 100 step 1
| summarize avgif(x, x%2 == 0)
```

|avgif_x|
|---|
|51|