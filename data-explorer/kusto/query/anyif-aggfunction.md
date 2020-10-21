---
title: anyif () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの anyif () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 032541f34af7d8cbc07e0c02a854ba6b66657f8c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248258"
---
# <a name="anyif-aggregation-function"></a>anyif () (集計関数)

[集計演算子](summarizeoperator.md)内のグループごとに、述語が "true" であるレコードを任意に選択します。 関数は、このような各レコードに対する式の値を返します。

> [!NOTE]
> この関数は、複合グループキーの値ごとに1つの列のサンプル値を取得する場合に便利です。これは、"true" である述語によって決まります。
> このような値が存在する場合、関数は null 以外の値または空でない値を返しようとします。

## <a name="syntax"></a>構文

`summarize``anyif` `(` *Expr*、*述語*`)`

## <a name="arguments"></a>引数

* *Expr*: 入力から選択された各レコードの式を返します。
* *述語*: 評価の対象となるレコードを示す述語。

## <a name="returns"></a>戻り値

集計関数は、集計 `anyif` 演算子の各グループからランダムに選択された各レコードに対して計算された式の値を返します。 *述語*が "true" を返すレコードのみが選択される可能性があります。 述語が "true" を返さない場合は、null 値が生成されます。

## <a name="examples"></a>例

300から6億への人口があるランダム大陸を表示します。

```kusto
Continents | summarize anyif(Continent, Population between (300000000 .. 600000000))
```

:::image type="content" source="images/aggfunction/any1.png" alt-text="任意1":::
