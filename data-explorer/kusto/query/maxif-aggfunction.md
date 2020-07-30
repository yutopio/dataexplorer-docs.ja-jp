---
title: maxif () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの maxif () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 471ca0e3d6623b77fd2d799949bfe060643798e2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346815"
---
# <a name="maxif-aggregation-function"></a>maxif () (集計関数)

*述語*がに評価されるグループ全体の最大値を返し `true` ます。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

「- [Max ()](max-aggfunction.md)関数」も参照してください。述語式が指定されていないグループ全体で最大値が返されます。

## <a name="syntax"></a>構文

`summarize``maxif(` *Expr* `,` *述語*`)`

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。 
* *述語*: true の場合、*式*の計算値が最大値に対してチェックされます。

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