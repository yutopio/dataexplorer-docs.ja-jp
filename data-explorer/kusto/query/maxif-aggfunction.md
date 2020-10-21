---
title: maxif () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの maxif () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4df30f50d82e0ad5af87acaaa88b55f151a2a77a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251710"
---
# <a name="maxif-aggregation-function"></a>maxif () (集計関数)

*述語*がに評価されるグループ全体の最大値を返し `true` ます。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

「- [Max ()](max-aggfunction.md) 関数」も参照してください。述語式が指定されていないグループ全体で最大値が返されます。

## <a name="syntax"></a>構文

`summarize``maxif(` *Expr* `,` *述語*`)`

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。 
* *述語*: true の場合、 *式* の計算値が最大値に対してチェックされます。

## <a name="returns"></a>戻り値

*述語*がに評価されるグループ全体の*Expr*の最大値 `true` 。

## <a name="examples"></a>例

```kusto
range x from 1 to 100 step 1
| summarize maxif(x, x < 50)
```

|maxif_x|
|---|
|49|