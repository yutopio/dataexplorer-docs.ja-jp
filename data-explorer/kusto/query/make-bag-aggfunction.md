---
title: make_bag() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでmake_bag() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 16f5f5663c4807a766d99c12020ff0a46c4db336
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512990"
---
# <a name="make_bag-aggregation-function"></a>make_bag() (集計関数)

グループ内`dynamic`の*Expr*のすべての値の (JSON) プロパティ バッグ (ディクショナリ) を返します。

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`summarize``make_bag(`*エクスプル*`,` [*最大サイズ*]`)`

**引数**

* *Expr*: 集計`dynamic`計算に使用される型の式。
* *MaxSize*は、返される要素の最大数に対するオプションの整数の制限です (既定値は*1048576)。* MaxSize 値は 1048576 を超えることはできません。

**注**

この関数の古いバージョンと古い`make_dictionary()`バージョン: *MaxSize* = 128 の既定の制限があります。

**戻り値**

プロパティ`dynamic`バッグ (ディクショナリ) であるグループ内の*Expr*のすべての値の (JSON) プロパティ バッグ (ディクショナリ) を返します。
非ディクショナリ値はスキップされます。
キーが複数の行に表示される場合は、任意の値 (このキーに指定できる値のうち) が選択されます。

**参照**

[bag_unpack()](bag-unpackplugin.md)プラグインを使用して、動的 JSON オブジェクトをプロパティバッグキーを使用して列に展開します。 

**使用例**

```kusto
let T = datatable(prop:string, value:string)
[
    "prop01", "val_a",
    "prop02", "val_b",
    "prop03", "val_c",
];
T
| extend p = pack(prop, value)
| summarize dict=make_bag(p)

```

|dict|
|----|
|{ "prop01": "val_a", "prop02": "val_b", "prop03": "val_c" } |

make_bag() 出力の袋キーを列に変換するには[、bag_unpack()](bag-unpackplugin.md)プラグインを使用します。 

```kusto
let T = datatable(prop:string, value:string)
[
    "prop01", "val_a",
    "prop02", "val_b",
    "prop03", "val_c",
];
T
| extend p = pack(prop, value)
| summarize bag=make_bag(p)
| evaluate bag_unpack(bag) 

```

|プロップ01|プロップ02|プロップ03|
|---|---|---|
|val_a|val_b|val_c|