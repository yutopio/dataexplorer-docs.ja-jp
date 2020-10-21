---
title: arg_min () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの arg_min () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/12/2019
ms.openlocfilehash: 5c3e7912839dd3258c4a3f96530fbf438bf4ef4f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245611"
---
# <a name="arg_min-aggregation-function"></a>arg_min () (集計関数)

は、 *Exprtominimize*を最小にし、 *ExprToReturn* の値を返す (または行全体を返す) グループ内の行を検索し `*` ます。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>構文

`summarize`[ `(` *Nameexprtominimize* `,` *NameExprToReturn* [ `,` ...] `)=` ] `arg_min` `(`*Exprtominimize*、 `*`  |  *ExprToReturn* [ `,` ...]`)`

## <a name="arguments"></a>引数

* *Exprtominimize*: 集計計算に使用される式。 
* *ExprToReturn*: *exprtominimize* が最小の場合に値を返すために使用される式。 返される式は、入力テーブルのすべての列を返すためのワイルドカード (*) である場合があります。
* *Nameexprtominimize*: *exprto最小化*を表す結果列の省略可能な名前です。
* *NameExprToReturn*: *ExprToReturn*を表す結果列の追加の省略可能な名前です。

## <a name="returns"></a>戻り値

は、 *Exprtominimize*を最小にし、 *ExprToReturn* の値を返す (または行全体を返す) グループ内の行を検索し `*` ます。

## <a name="examples"></a>例

各製品の最も安価な仕入先を表示:

```kusto
Supplies | summarize arg_min(Price, Supplier) by Product
```

仕入先名だけでなく、すべての詳細を表示します。

```kusto
Supplies | summarize arg_min(Price, *) by Product
```

各大陸の southernmost 市とその国を検索します。

```kusto
PageViewLog 
| summarize (latitude, min_lat_City, min_lat_country)=arg_min(latitude, City, country) 
    by continent
```

:::image type="content" source="images/arg-min-aggfunction/arg-min.png" alt-text="Arg min":::
