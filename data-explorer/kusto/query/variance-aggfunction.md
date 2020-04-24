---
title: variance() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの variance() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5b1d2ea47060ecede855a3fb419bbbfe2bf0b538
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504711"
---
# <a name="variance-aggregation-function"></a>分散()(集計関数)

グループを[標本](https://en.wikipedia.org/wiki/Sample_%28statistics%29)と見なして、グループ全体の*Expr*の分散を計算します。 

* 使用する数式:![代替テキスト](./images/aggregations/variance-sample.png "分散サンプル")

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`variance(`*エクスプの*要約`)`

**引数**

* *Expr*: 集計の計算に使用する式。 

**戻り値**

グループ全体にわたる*Expr*の分散値。
 
**使用例**

```kusto
range x from 1 to 5 step 1
| summarize make_list(x), variance(x) 
```

|list_x|variance_x|
|---|---|
|[ 1, 2, 3, 4, 5]|2.5|