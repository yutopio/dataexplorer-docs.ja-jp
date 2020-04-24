---
title: varianceif() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの varianceif() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9dfebb3796f07dec6c91d36d788a018f84f70961
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504677"
---
# <a name="varianceif-aggregation-function"></a>分散関数(集計関数)

*述語*が 評価されるグループ全体の*Expr*の[分散](variance-aggfunction.md)を`true`計算します。

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`varianceif(` *Expr*`, `*述語*の要約`)`

**引数**

* *Expr*: 集計の計算に使用する式。 
* *述語*: true の場合 *、Expr*計算値が分散に追加される述語。

**戻り値**

*述語*が 評価されるグループ全体にわたる*Expr*の`true`分散値。
 
**使用例**

```kusto
range x from 1 to 100 step 1
| summarize varianceif(x, x%2 == 0)

```

|varianceif_x|
|---|
|850|