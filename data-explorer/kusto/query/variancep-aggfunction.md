---
title: variancep() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの variancep() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 386244806a6fcb3f321eb1a6b40595dc71b2b413
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504592"
---
# <a name="variancep-aggregation-function"></a>分散関数(集計関数)

グループを[母集団](https://en.wikipedia.org/wiki/Statistical_population)と見なして、グループ全体の*Expr*の分散を計算します。 

* 使用する数式:![代替テキスト](./images/aggregations/variance-population.png "分散母集団")

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`variancep(`*エクスプの*要約`)`

**引数**

* *Expr*: 集計の計算に使用する式。 

**戻り値**

グループ全体にわたる*Expr*の分散値。
 
**使用例**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variancep(x) 
```

|list_x|variance_x|
|---|---|
|[ 1, 2, 3, 4, 5]|2|