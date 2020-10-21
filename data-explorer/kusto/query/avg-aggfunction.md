---
title: avg () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの avg () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 949bd463f57b9eb0b674fe780aa50e78e30926a2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248038"
---
# <a name="avg-aggregation-function"></a>avg () (集計関数)

グループ全体の *Expr* の平均を計算します。 

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>構文

集計の `avg(` *Expr*`)`

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。 値を持つレコード `null` は無視され、計算には含まれません。

## <a name="returns"></a>戻り値

グループ全体の *Expr* の平均値。
 