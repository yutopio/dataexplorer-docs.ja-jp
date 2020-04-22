---
title: arg_min() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでarg_min() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/12/2019
ms.openlocfilehash: 4531bd498cf9d9053d57d8b463c7b0adfcc9bf8d
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663981"
---
# <a name="arg_min-aggregation-function"></a>arg_min() (集計関数)

*ExprToMinimize*を最小化するグループ内の行を検索し *、ExprToReturn*の値を返`*`します (または行全体を返します)。

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`summarize`[`(`*名前を付けるため名前を最小化*`,`*する Expr 戻す*] [.]`,``)=` `arg_min` ] `(`*エクスプルト最小化*, `*`  | *エクスプルトリターン*[`,` ..`)`

**引数**

* *ExprToMinimize*: 集計計算に使用される式。 
* *ExprToReturn*: *ExprToMinimize*が最小値の場合に値を返すために使用される式。 返す式は、入力テーブルのすべての列を返すワイルドカード (*) である場合があります。
* *名前 ExprprToMinimize*: *ExprToMinimize*を表す結果列の省略可能な名前。
* *名前を指定します*。 *ExprToReturn*

**戻り値**

*ExprToMinimize*を最小化するグループ内の行を検索し *、ExprToReturn*の値を返`*`します (または行全体を返します)。

**使用例**

各製品の最も安いサプライヤーを表示します。

```kusto
Supplies | summarize arg_min(Price, Supplier) by Product
```

サプライヤー名だけでなく、すべての詳細を表示します。

```kusto
Supplies | summarize arg_min(Price, *) by Product
```

その国で、各大陸の最南端の都市を見つける:

```kusto
PageViewLog 
| summarize (latitude, min_lat_City, min_lat_country)=arg_min(latitude, City, country) 
    by continent
```

:::image type="content" source="images/aggregations/arg-min.png" alt-text="アルグ分":::
