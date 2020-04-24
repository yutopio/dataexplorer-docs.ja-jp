---
title: make_list() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでmake_list() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: e46dbfac7b8c819f66d469c160452ec4dddfb769
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512752"
---
# <a name="make_list-aggregation-function"></a>make_list() (集計関数)

グループ内にある*式*のすべての値の `dynamic` (JSON) 配列を返します。

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`summarize``make_list(`*エクスプル*`,` [*最大サイズ*]`)`

**引数**

* *Expr*: 集計の計算に使用する式。
* *MaxSize*は、返される要素の最大数に対するオプションの整数の制限です (既定値は*1048576)。* MaxSize 値は 1048576 を超えることはできません。

> [!NOTE]
> この関数の古いバージョンと古い`makelist()`バージョン: *MaxSize* = 128 の既定の制限があります。

**戻り値**

グループ内にある*式*のすべての値の `dynamic` (JSON) 配列を返します。
演算子への入力が`summarize`ソートされていない場合、結果の配列内の要素の順序は未定義です。
演算子への入力が`summarize`ソートされている場合、結果の配列内の要素の順序は入力の順序を追跡します。

> [!TIP]
> 演算子を[`mv-apply`](./mv-applyoperator.md)使用して、キーによって順序付けされたリストを作成します。 [こちら](./mv-applyoperator.md#using-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key)の例を参照してください。

**参照**

[`make_list_if`](./makelistif-aggfunction.md)演算子は、`make_list`に似ていますが、述語も受け入れます。