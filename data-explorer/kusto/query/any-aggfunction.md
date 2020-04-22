---
title: any() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの any() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0726b185c22cd84c93e28601a823a35d9685d96d
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663945"
---
# <a name="any-aggregation-function"></a>any() (集計関数)

[集計演算子](summarizeoperator.md)でグループごとに 1 つのレコードを任意に選択し、各レコードに対して 1 つ以上の式の値を返します。

**構文**

`summarize``any` *Expr* `,` *Expr2* ( エクスプル [ Expr2 ..]) | `(``*``)`

**引数**

* *Expr*: 返す入力から選択された各レコードに対する式。
* *Expr2* .. *ExprN*: 追加の式。

**戻り値**

集計`any`関数は、集計演算子の各グループからランダムに選択された各レコードについて計算された式の値を返します。

引数を`*`指定すると、式が、集計演算子に対する入力のすべての列であるかのように動作し、グループ化列がある場合は、その列を禁止します。

**解説**

この関数は、複合グループ キーの値ごとに 1 つ以上の列のサンプル値を取得する場合に便利です。

関数が単一の列参照で提供されると、null 以外の値が存在する場合は、null 以外の値を返そうとします。

この関数のランダムな性質の結果として、`summarize`演算子の単一のアプリケーションで複数回使用することは、複数の式で 1 回使用することとは異なります。 前者は各アプリケーションで異なるレコードを選択し、後者はすべての値が単一のレコード(個別のグループ単位)で計算されることを保証します。

**使用例**

ランダム大陸を表示:

```kusto
Continents | summarize any(Continent)
```

:::image type="content" source="images/aggregations/any1.png" alt-text="任意の 1":::

ランダムレコードのすべての詳細を表示します。

```kusto
Continents | summarize any(*)
```

:::image type="content" source="images/aggregations/any2.png" alt-text="任意の2":::

各ランダム大陸のすべての詳細を表示します。

```kusto
Continents | summarize any(*) by Continent
```

:::image type="content" source="images/aggregations/any3.png" alt-text="任意の3":::
