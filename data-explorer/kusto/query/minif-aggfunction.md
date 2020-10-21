---
title: minif () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの minif () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f6932a50d59ee3df73857bfd4230faaa2e10dd2b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251004"
---
# <a name="minif-aggregation-function"></a>minif () (集計関数)

*述語*がに評価されるグループ全体の最小値を返し `true` ます。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

「- [Min ()](min-aggfunction.md) 関数」を参照してください。この関数は、述語式が指定されていないグループ全体の最小値を返します。

## <a name="syntax"></a>構文

`summarize``minif(` *Expr* `,` *述語*`)`

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。
* *述語*: true の場合、 *式* の計算値が最小になるかどうかがチェックされます。

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