---
title: sum () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの sum () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: cb2bb49a0580819a172657ba4258fad08457d1d7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251362"
---
# <a name="sum-aggregation-function"></a>sum () (集計関数)

グループ全体の *Expr* の合計を計算します。 

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>構文

集計の `sum(` *Expr*`)`

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。 

## <a name="returns"></a>戻り値

グループ全体の *Expr* の合計値。
 