---
title: tdigest_merge () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの tdigest_merge () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 14b6b1c5ed31c312065e7641783bd1dc524993d6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250641"
---
# <a name="tdigest_merge-aggregation-function"></a>tdigest_merge () (集計関数)

グループ全体で tdigest の結果をマージします。 

* 集計のコンテキスト内でのみ使用できます[。](summarizeoperator.md)

基になるアルゴリズム (T ダイジェスト) と推定エラーの詳細については、 [こちら](percentiles-aggfunction.md#estimation-error-in-percentiles)を参照してください。

## <a name="syntax"></a>構文

`tdigest_merge(` *Expr* `)` を集計します。

集計 `tdigest_merge(` *式* `)` -エイリアス。

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。 

## <a name="returns"></a>戻り値

グループ全体の *Expr* の結合された tdigest 値。
 

**ヒント**

1) 関数 [ `percentile_tdigest()` ] (percentile-tdigestfunction.md) を使用できます。

2) 同じグループに含まれるすべての tdigests は、同じ種類である必要があります。