---
title: make_list_with_nulls() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでmake_list_with_nulls() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
ms.openlocfilehash: 4b039008c5969cf02187d69a3486b09e04ec41ae
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512871"
---
# <a name="make_list_with_nulls-aggregation-function"></a>make_list_with_nulls() (集計関数)

グループ内`dynamic`の*Expr*のすべての値 (NULL 値を含む) の配列 (JSON) を返します。

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`summarize``make_list_with_nulls(`*エクスプル*`)`

**引数**

* *Expr*: 集計の計算に使用する式。

**戻り値**

グループ内`dynamic`の*Expr*のすべての値 (NULL 値を含む) の配列 (JSON) を返します。
演算子への入力が`summarize`ソートされていない場合、結果の配列内の要素の順序は未定義です。
演算子への入力が`summarize`ソートされている場合、結果の配列内の要素の順序は入力の順序を追跡します。

> [!TIP]
> 演算子を[`mv-apply`](./mv-applyoperator.md)使用して、キーによって順序付けされたリストを作成します。 [こちら](./mv-applyoperator.md#using-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key)の例を参照してください。
