---
title: stdevif () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの stdevif () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a158a623768a7beb6ec497ca8d8467aecd7c3b61
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342820"
---
# <a name="stdevif-aggregation-function"></a>stdevif () (集計関数)

*述語*がに評価されるグループ全体の*Expr*の[stdev](stdev-aggfunction.md)を計算し `true` ます。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>構文

`stdevif(` *Expr* `, ` *述語*の集計`)`

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。 
* *述語*: true の場合、*式*の計算値が標準偏差に追加されます。

## <a name="returns"></a>戻り値

*述語*がに評価されるグループ全体の*Expr*の標準偏差値 `true` 。
 
## <a name="examples"></a>例

```kusto
range x from 1 to 100 step 1
| summarize stdevif(x, x%2 == 0)

```

|stdevif_x|
|---|
|29.1547594742265|