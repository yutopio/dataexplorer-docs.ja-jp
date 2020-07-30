---
title: varianceif () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの varianceif () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bf1009d2d269bf21ea5ae14a9c828724d8bf8c70
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338475"
---
# <a name="varianceif-aggregation-function"></a>varianceif () (集計関数)

*述語*がに評価されるグループ全体の*Expr*の[分散](variance-aggfunction.md)を計算し `true` ます。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>構文

`varianceif(` *Expr* `, ` *述語*の集計`)`

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。 
* *述語*: true の場合、*式*の計算値が分散に追加されます。

## <a name="returns"></a>戻り値

*述語*がに評価されるグループ全体の*Expr*の分散値 `true` 。
 
## <a name="examples"></a>例

```kusto
range x from 1 to 100 step 1
| summarize varianceif(x, x%2 == 0)

```

|varianceif_x|
|---|
|850|