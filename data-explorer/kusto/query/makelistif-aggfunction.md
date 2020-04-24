---
title: make_list_if() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでmake_list_if() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b34c1dad7be709145c622c97b357734c25292bba
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512735"
---
# <a name="make_list_if-aggregation-function"></a>make_list_if() (集計関数)

グループ内`dynamic`の*Expr*のすべての値の (JSON) 配列を返します*Predicate*`true`。

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`summarize``make_list_if(`*エクスプル*,*述語*[`,` *最大サイズ*]`)`

**引数**

* *Expr*: 集計の計算に使用する式。
* *述語*: *Expr* `true`を結果に追加するために評価する必要がある述語。
* *MaxSize*は、返される要素の最大数に対するオプションの整数の制限です (既定値は*1048576)。* MaxSize 値は 1048576 を超えることはできません。

**戻り値**

グループ内`dynamic`の*Expr*のすべての値の (JSON) 配列を返します*Predicate*`true`。
演算子への入力が`summarize`ソートされていない場合、結果の配列内の要素の順序は未定義です。
演算子への入力が`summarize`ソートされている場合、結果の配列内の要素の順序は入力の順序を追跡します。

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
| summarize make_list_if(name, strlen(name) > 4)
```

|list_name|
|----|
|[ジョージ、リンゴ]|

**参照**

[`make_list`](./makelist-aggfunction.md)述語式なしで同じことを行う関数。