---
title: any () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのすべての () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c4d718cfb46e3a404c943d579feaf4733499ab3e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248216"
---
# <a name="any-aggregation-function"></a>any () (集計関数)

は、 [集計演算子](summarizeoperator.md)のグループごとに1つのレコードを任意に選択し、そのようなレコードに対して1つ以上の式の値を返します。

## <a name="syntax"></a>構文

`summarize``any` `(` (*Expr* [ `,` *Expr2* ...]) | `*``)`

## <a name="arguments"></a>引数

* *Expr*: 入力から選択された各レコードの式を返します。
* *Expr2* . *ExprN*: 追加の式。

## <a name="returns"></a>戻り値

集計関数は、集計 `any` 演算子の各グループからランダムに選択された、各レコードに対して計算された式の値を返します。

引数が指定されている場合、 `*` 関数は、集計演算子への入力のすべての列が、group by 列がない場合は、それを除いたものとして動作します。

**解説**

この関数は、複合グループキーの値ごとに1つ以上の列のサンプル値を取得する場合に便利です。

関数に1つの列参照が指定されている場合、その値が存在する場合は、null 以外の値または空でない値が返されます。

この関数にはランダムな性質があるため、演算子の1つのアプリケーションで複数回使用 `summarize` することは、複数の式で1回だけ使用することと同じではありません。 前者では、各アプリケーションで別のレコードを選択することができますが、後者の場合は、すべての値が1つのレコードに対して計算されます (個別のグループにつき)。

## <a name="examples"></a>例

ランダムな大陸を表示:

```kusto
Continents | summarize any(Continent)
```

:::image type="content" source="images/aggfunction/any1.png" alt-text="任意1":::

ランダムなレコードのすべての詳細を表示します。

```kusto
Continents | summarize any(*)
```

:::image type="content" source="images/aggfunction/any2.png" alt-text="任意1":::

各ランダム大陸のすべての詳細を表示します。

```kusto
Continents | summarize any(*) by Continent
```

:::image type="content" source="images/aggfunction/any3.png" alt-text="任意1":::
