---
title: min () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの min () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/24/2019
ms.openlocfilehash: c6cd5b219b1f89e68d34b37b21135e5ec02d6ee8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248946"
---
# <a name="min-aggregation-function"></a>min () (集計関数)

グループ全体の最小値を返します。 

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>構文

`summarize``min(` *Expr*`)`

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。 

## <a name="returns"></a>戻り値

グループ全体の *Expr* の最小値。
 
> [!TIP]
> これにより、それぞれの最小値または最大値が得られます。たとえば、最高価格や最低価格などです。 ただし、行に他の列が必要な場合は (価格が最も低い仕入先の名前など)、 [arg_max](arg-max-aggfunction.md) または [arg_min](arg-min-aggfunction.md)を使用します。