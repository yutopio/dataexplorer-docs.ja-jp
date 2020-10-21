---
title: make_bag () (集計関数)-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの make_bag () 集計関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 41eb9703f1718bb43b41927f9ec4d7cac52d67c4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249935"
---
# <a name="make_bag-aggregation-function"></a>make_bag () (集計関数)

`dynamic`グループ内ののすべての値の (JSON) プロパティバッグ (ディクショナリ) を返し *`Expr`* ます。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>構文

`summarize``make_bag(` *`Expr`* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>引数

* *Expr*: `dynamic` 集計計算に使用される型の式です。
* *MaxSize* は、返される要素の最大数に対する整数の制限 (省略可能) です。 既定値は *1048576*です。 MaxSize 値は *1048576*を超えることはできません。

**注**

従来の関数と古いバージョンの関数では、 `make_dictionary()` *MaxSize* = 128 の既定の制限が設定されています。

## <a name="returns"></a>戻り値

`dynamic`グループ内ののすべての値の (JSON) プロパティバッグ (ディクショナリ) を返し *`Expr`* ます。これは、プロパティバッグです。
ディクショナリ以外の値はスキップされます。
1つのキーが複数の行に表示されている場合は、このキーに指定できる値から任意の値が選択されます。

## <a name="see-also"></a>関連項目

動的 JSON オブジェクトをプロパティバッグキーを使用する列に展開するには、 [bag_unpack ()](bag-unpackplugin.md) プラグインを使用します。 

## <a name="examples"></a>例

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
|{"prop01": "val_a", "prop02": "val_b", "prop03": "val_c"} |

Make_bag () 出力のバッグキーを列に変換するには、 [bag_unpack ()](bag-unpackplugin.md) プラグインを使用します。 

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

|prop01|prop02|prop03|
|---|---|---|
|val_a|val_b|val_c|
