---
title: make_list () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの make_list () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: ecfcaa39195caec06184b966403bd6655a00b714
ms.sourcegitcommit: 62476f682b7812cd9cff7e6958ace5636ee46755
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2020
ms.locfileid: "92169542"
---
# <a name="make_list-aggregation-function"></a>make_list () (集計関数)

グループ内にある*式*のすべての値の `dynamic` (JSON) 配列を返します。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>Syntax

`summarize``make_list(` *Expr* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。
* *MaxSize* は、返される要素の最大数に対する整数制限 (省略可能) です (既定値は *1048576*)。 MaxSize 値は1048576を超えることはできません。

> [!NOTE]
> この関数の従来型および旧形式のバリアントでは、 `makelist()` *MaxSize* = 128 の既定の制限が設定されています。

## <a name="returns"></a>戻り値

グループ内にある*式*のすべての値の `dynamic` (JSON) 配列を返します。
演算子への入力 `summarize` が並べ替えられていない場合、結果として得られる配列内の要素の順序は定義されません。
演算子への入力が並べ替えられている場合、結果として `summarize` 得られる配列内の要素の順序によって、入力の値が追跡されます。

> [!TIP]
> 任意の [`array_sort_asc()`](./arraysortascfunction.md) [`array_sort_desc()`](./arraysortdescfunction.md) キーで順序付きリストを作成するには、関数または関数を使用します。

## <a name="examples"></a>例

### <a name="one-column"></a>1つの列

最も簡単な例は、1つの列からリストを作成することです。

```kusto
let shapes = datatable (name: string, sideCount: int)
[
    "triangle", 3,
    "square", 4,
    "rectangle", 4,
    "pentagon", 5,
    "hexagon", 6,
    "heptagon", 7,
    "octogon", 8,
    "nonagon", 9,
    "decagon", 10
];
shapes
| summarize mylist = make_list(name)
```

|mylist|
|---|
|["三角形"、"square"、"rectangle"、"五角形"、"六角形"、"heptagon"、"octogon"、"nonagon"、"decagon"]|

### <a name="using-the-by-clause"></a>' By ' 句を使用します。

次のクエリでは、句を使用してグループ化し `by` ます。

```kusto
let shapes = datatable (name: string, sideCount: int)
[
    "triangle", 3,
    "square", 4,
    "rectangle", 4,
    "pentagon", 5,
    "hexagon", 6,
    "heptagon", 7,
    "octogon", 8,
    "nonagon", 9,
    "decagon", 10
];
shapes
| summarize mylist = make_list(name) by isEvenSideCount = sideCount % 2 == 0
```

|mylist|isEvenSideCount|
|---|---|
|false|["三角形"、"五角形"、"heptagon"、"nonagon"]|
|true|["square"、"rectangle"、"六角形"、"octogon"、"decagon"]|

### <a name="packing-a-dynamic-object"></a>動的オブジェクトのパッキング

次のクエリに示すように、列に動的オブジェクトを [パック](./packfunction.md) してから、リストを作成することができます。

```kusto
let shapes = datatable (name: string, sideCount: int)
[
    "triangle", 3,
    "square", 4,
    "rectangle", 4,
    "pentagon", 5,
    "hexagon", 6,
    "heptagon", 7,
    "octogon", 8,
    "nonagon", 9,
    "decagon", 10
];
shapes
| extend d = pack("name", name, "sideCount", sideCount)
| summarize mylist = make_list(d) by isEvenSideCount = sideCount % 2 == 0
```

|mylist|isEvenSideCount|
|---|---|
|false|[{"name": "三角形"、"sideCount": 3}、{"name": "五角形"、"sideCount": 5}、{"name": "heptagon"、"sideCount": 7}、{"name": "nonagon"、"sideCount": 9}]|
|true|[{"name": "square"、"sideCount": 4}、{"name": "rectangle"、"sideCount": 4}、{"name": "六角形"、"sideCount": 6}、{"name": "octogon"、"sideCount": 8}、{"name": "decagon"、"sideCount":10}]|

## <a name="see-also"></a>こちらもご覧ください

[`make_list_if`](./makelistif-aggfunction.md) 演算子はに似 `make_list` ていますが、述語も受け入れる点が異なります。
