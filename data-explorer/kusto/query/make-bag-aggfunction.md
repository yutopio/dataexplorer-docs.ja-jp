---
title: make_bag () (集計関数)-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの make_bag () 集計関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3258a847a526e0e3b6ac8f0186b0a1aaabc3ffe5
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103205"
---
# <a name="make_bag-aggregation-function"></a>make_bag () (集計関数)

`dynamic`グループ内ののすべての値の (JSON) プロパティバッグ (ディクショナリ) を返し *`Expr`* ます。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>構文

`summarize``make_bag(` *`Expr`* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>引数

* *Expr*: `dynamic` 集計計算に使用される型の式です。
* *MaxSize* は、返される要素の最大数に対する整数の制限 (省略可能) です。 既定値は *1048576*です。 MaxSize 値は *1048576*を超えることはできません。

**注:**

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
