---
title: stdevif() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで stdevif() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4a64cf1bb69860a2a8bd64de91cb00c2f0ec296f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506972"
---
# <a name="stdevif-aggregation-function"></a>スデヴィフ() (集計関数)

*述語*が 評価されるグループ全体で`true`*Expr*の[stdev](stdev-aggfunction.md)を計算します。

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`stdevif(` *Expr*`, `*述語*の要約`)`

**引数**

* *Expr*: 集計の計算に使用する式。 
* *述語*: true の場合 *、Expr*計算値が標準偏差に追加される述語。

**戻り値**

*述語*が 評価されるグループ全体の*Expr*の標準偏差`true`値 。
 
**使用例**

```kusto
range x from 1 to 100 step 1
| summarize stdevif(x, x%2 == 0)

```

|stdevif_x|
|---|
|29.1547594742265|