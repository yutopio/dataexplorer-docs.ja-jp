---
title: max () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの max () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: cd53f0701064d95ab2d088e774f49b06f8d323f7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251066"
---
# <a name="max-aggregation-function"></a>max () (集計関数)

グループ全体の最大値を返します。 

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>構文

`summarize``max(` *Expr*`)`

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。 

## <a name="returns"></a>戻り値

グループ全体の *Expr* の最大値。
 
> [!TIP]
> これにより、それぞれの最小値または最大値が得られます。たとえば、最高価格や最低価格などです。
> ただし、行に他の列が必要な場合は (価格が最も低い仕入先の名前など)、 [arg_max](arg-max-aggfunction.md) または [arg_min](arg-min-aggfunction.md)を使用します。