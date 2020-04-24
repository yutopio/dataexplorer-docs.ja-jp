---
title: avgif() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの avgif() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 61352be628b7c5a05085c092d0c022deaa0d9b6e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518260"
---
# <a name="avgif-aggregation-function"></a>avgif() (集計関数)

*述語*が 評価されるグループ全体の*Expr*の[平均](avg-aggfunction.md)を`true`計算します。

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`avgif(` *Expr*`, `*述語*の要約`)`

**引数**

* *Expr*: 集計の計算に使用する式。 値を`null`持つレコードは無視され、計算には含まれません。
* *述語*: true の場合 *、Expr*計算値が平均に加算される述語。

**戻り値**

*述語*が 評価されるグループ全体の*Expr*の`true`平均値。
 
**使用例**

```kusto
range x from 1 to 100 step 1
| summarize avgif(x, x%2 == 0)
```

|avgif_x|
|---|
|51|