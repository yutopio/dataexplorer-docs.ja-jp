---
title: sumif() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで sumif() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7d2c96f73b404e8d9acbe9da9defecd6bf1bbf3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506666"
---
# <a name="sumif-aggregation-function"></a>sumif() (集計関数)

*述語*が 評価される*Expr*の合計`true`を返します。

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

述語式を使用せずに行を合計する[sum()](sum-aggfunction.md)関数を使用することもできます。

**構文**

`sumif(` *Expr*`,`*述語*の要約`)`

**引数**

* *Expr*: 集計計算の式。 
* *述語*: 述語で、true の場合 *、Expr*の計算値が合計に加算されます。 

**戻り値**

*述語*が 評価される*Expr*の合計`true`値。

> [!TIP]
> `where filter | summarize sum(expr)` の代わりの `summarize sumif(expr, filter)` の使用

**例**

```kusto
let T = datatable(name:string, day_of_birth:long)
[
   "John", 9,
   "Paul", 18,
   "George", 25,
   "Ringo", 7
];
T
| summarize sumif(day_of_birth, strlen(name) > 4)
```

|sumif_day_of_birth|
|----|
|32|