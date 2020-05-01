---
title: make_set () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの make_set () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: e6eb481423e31e4dfa1b4e6c738ffb525e9aaef7
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618405"
---
# <a name="make_set-aggregation-function"></a>make_set () (集計関数)

グループ内にある*式*で使用される個別の値セットの `dynamic` (JSON) 配列を返します 

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

**構文**

`summarize``make_set(` *Expr* [`,` *MaxSize*]`)`

**引数**

* *Expr*: 集計計算用の式。
* *MaxSize*は、返される要素の最大数に対する整数制限 (省略可能) です (既定値は*1048576*)。 MaxSize 値は1048576を超えることはできません。

> [!NOTE]
> この関数の従来型および旧形式の`makeset()`バリアントでは、 *MaxSize* = 128 の既定の制限が設定されています。

**戻り値**

グループ内にある*式*で使用される個別の値セットの `dynamic` (JSON) 配列を返します 
配列の並べ替え順序が定義されていません。

> [!TIP]
> 個別の値をカウントするだけの場合は、 [dcount ()](dcount-aggfunction.md)を使用します。

**例**

```kusto
PageViewLog 
| summarize countries=make_set(country) by continent
```

:::image type="content" source="images/makeset-aggfunction/makeset.png" alt-text="Makeset":::

**参照**

* 反対[`mv-expand`](./mvexpandoperator.md)の関数には演算子を使用します。
* [`make_set_if`](./makesetif-aggfunction.md)演算子はに`make_set`似ていますが、述語も受け入れる点が異なります。