---
title: make_bag_if() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの make_bag_if() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 201de637df387bb8925995a5e52c048255d1535c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512973"
---
# <a name="make_bag_if-aggregation-function"></a>make_bag_if() (集計関数)

グループ内`dynamic`の*Expr*のすべての値の (JSON) プロパティ バッグ (ディクショナリ) を*Predicate*返します`true`。

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`summarize``make_bag_if(`*エクスプル*,*述語*[`,` *最大サイズ*]`)`

**引数**

* *Expr*: 集計`dynamic`計算に使用される型の式。
* *述語*: *Expr* `true`を結果に追加するために評価する必要がある述語。
* *MaxSize*は、返される要素の最大数に対するオプションの整数の制限です (既定値は*1048576)。* MaxSize 値は 1048576 を超えることはできません。

**戻り値**

Predicate`dynamic`が評価されるプロパティ バッグ (ディクショナリ) であるグループ内の*Expr*のすべての値の (JSON) プロパティ バッグ*Predicate*(ディクショナリ)`true`を返します。
非ディクショナリ値はスキップされます。
キーが複数の行に表示される場合は、任意の値 (このキーに指定できる値のうち) が選択されます。

**参照**

[`make_bag`](./make-bag-aggfunction.md)述語式なしで同じことを行う関数。

**使用例**

```kusto
let T = datatable(prop:string, value:string, predicate:bool)
[
    "prop01", "val_a", true,
    "prop02", "val_b", false,
    "prop03", "val_c", true
];
T
| extend p = pack(prop, value)
| summarize dict=make_bag_if(p, predicate)

```

|dict|
|----|
|{ "prop01": "val_a", "prop03": "val_c" } |

make_bag_if() 出力内のバッグキーを列に変換するには[、bag_unpack](bag-unpackplugin.md)プラグインを使用します。 

```kusto
let T = datatable(prop:string, value:string, predicate:bool)
[
    "prop01", "val_a", true,
    "prop02", "val_b", false,
    "prop03", "val_c", true
];
T
| extend p = pack(prop, value)
| summarize bag=make_bag_if(p, predicate)
| evaluate bag_unpack(bag) 

```

|プロップ01|プロップ03|
|---|---|
|val_a|val_c|