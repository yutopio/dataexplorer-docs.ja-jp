---
title: anyif() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの anyif() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5e19332cf464fcad1715f4feddfe7a2c5765bb0d
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663997"
---
# <a name="anyif-aggregation-function"></a>anyif() (集計関数)

述語が真である[集計演算子](summarizeoperator.md)のグループごとに 1 つのレコードを任意に選択し、各レコードに対する式の値を返します。

**構文**

`summarize`エクスプル ,*述語*) ' *Expr* `anyif` `(`

**引数**

* *Expr*: 返す入力から選択された各レコードに対する式。
* *述語*: 評価対象と考えられるレコードを示す述語。

**戻り値**

集計`anyif`関数は、集計演算子の各グループからランダムに選択された各レコードについて計算された式の値を返します。 *Predicate*が true を返すレコードのみを選択できます (述語が true を返さない場合は、NULL 値が生成されます)。

**解説**

この関数は、複合グループ キーの値ごとに 1 列のサンプル値を取得する場合に便利です。

関数は、null 以外の値または空でない値が存在する場合は、値を返そうとします。

**使用例**

3億から6億の人口を持つランダムな大陸を表示します。

```kusto
Continents | summarize anyif(Continent, Population between (300000000 .. 600000000))
```

:::image type="content" source="images/aggregations/any1.png" alt-text="任意の 1":::
