---
title: make_list_if () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの make_list_if () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9090e752f018c4abcce759c37a8ecb3571e2fbd6
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91102907"
---
# <a name="make_list_if-aggregation-function"></a>make_list_if () (集計関数)

述語が `dynamic` に評価される、グループ内の*Expr*のすべての値の (JSON *Predicate* ) 配列を返し `true` ます。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>構文

`summarize``make_list_if(` *Expr*、*述語*[ `,` *MaxSize*]`)`

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。
* *述語*: `true` 結果に *Expr* を追加するために、に評価する必要がある述語。
* *MaxSize* は、返される要素の最大数に対する整数制限 (省略可能) です (既定値は *1048576*)。 MaxSize 値は1048576を超えることはできません。

## <a name="returns"></a>戻り値

述語が `dynamic` に評価される、グループ内の*Expr*のすべての値の (JSON *Predicate* ) 配列を返し `true` ます。
演算子への入力 `summarize` が並べ替えられていない場合、結果として得られる配列内の要素の順序は定義されません。
演算子への入力が並べ替えられている場合、結果として `summarize` 得られる配列内の要素の順序によって、入力の値が追跡されます。

## <a name="example"></a>例

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
|["ジョージ", "Ringo"]|

## <a name="see-also"></a>関連項目

[`make_list`](./makelist-aggfunction.md) 述語式を使用せずに同じを実行する関数。