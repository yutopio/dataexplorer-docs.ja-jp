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
ms.openlocfilehash: fed2616f5fbd32b1c80f936d5689261467a9dc5e
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224836"
---
# <a name="make_list-aggregation-function"></a>make_list () (集計関数)

グループ内にある*式*のすべての値の `dynamic` (JSON) 配列を返します。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

**構文**

`summarize``make_list(` *Expr* [ `,` *MaxSize*]`)`

**引数**

* *Expr*: 集計計算に使用される式です。
* *MaxSize*は、返される要素の最大数に対する整数制限 (省略可能) です (既定値は*1048576*)。 MaxSize 値は1048576を超えることはできません。

> [!NOTE]
> この関数の従来型および旧形式のバリアントでは、 `makelist()` *MaxSize* = 128 の既定の制限が設定されています。

**戻り値**

グループ内にある*式*のすべての値の `dynamic` (JSON) 配列を返します。
演算子への入力 `summarize` が並べ替えられていない場合、結果として得られる配列内の要素の順序は定義されません。
演算子への入力が並べ替えられている場合、結果として `summarize` 得られる配列内の要素の順序によって、入力の値が追跡されます。

> [!TIP]
> [`mv-apply`](./mv-applyoperator.md)キーによって順序付けられたリストを作成するには、演算子を使用します。 [こちら](./mv-applyoperator.md#using-the-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key)の例を参照してください。

**参照**

[`make_list_if`](./makelistif-aggfunction.md)演算子はに似 `make_list` ていますが、述語も受け入れる点が異なります。