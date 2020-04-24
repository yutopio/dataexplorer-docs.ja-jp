---
title: minif() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの minif() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0aad254ec01e83bdb07734e5b309c1450512b446
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512361"
---
# <a name="minif-aggregation-function"></a>minif() (集計関数)

*Predicate*が 評価されるグループ全体の最小値を`true`返します。

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

述語式を使用せずにグループ全体の最小値を返す[min()](min-aggfunction.md)関数も参照してください。

**構文**

`summarize``minif(`*エクスプラ*`,`*述部*`)`

**引数**

* *Expr*: 集計の計算に使用する式。
* *述語*: 述語が true の場合 *、Expr*計算値が最小値をチェックします。

**戻り値**

*述語*が 評価されるグループ全体の*Expr*の最小値`true`。

**使用例**

```kusto
range x from 1 to 100 step 1
| summarize minif(x, x > 50)
```

|minif_x|
|---|
|51|