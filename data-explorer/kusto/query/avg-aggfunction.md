---
title: avg () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの avg () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: f058af075a856d12a2a6a81419f32b6efbd9ea16
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349399"
---
# <a name="avg-aggregation-function"></a>avg () (集計関数)

グループ全体の*Expr*の平均を計算します。 

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>構文

集計の `avg(` *Expr*`)`

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。 値を持つレコード `null` は無視され、計算には含まれません。

## <a name="returns"></a>戻り値

グループ全体の*Expr*の平均値。
 