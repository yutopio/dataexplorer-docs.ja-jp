---
title: sum () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの sum () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 3053a2c3bd423a2e1b8a2690bcdf54de9f1a1e36
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350827"
---
# <a name="sum-aggregation-function"></a>sum () (集計関数)

グループ全体の*Expr*の合計を計算します。 

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>構文

集計の `sum(` *Expr*`)`

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。 

## <a name="returns"></a>戻り値

グループ全体の*Expr*の合計値。
 