---
title: beta_pdf() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでbeta_pdf() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 53b86d88b05cc6c5cc31f1e3bbb9e3e712eed7f8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517869"
---
# <a name="beta_pdf"></a>beta_pdf()

確率密度のベータ関数を返します。

```kusto
beta_pdf(0.2, 10.0, 50.0)
```

通常、ベータ分布は複数の標本を対象として割合の変化を分析する場合などに使用します。たとえば、複数の人が 1 日のうちにテレビを見ている時間の割合を算出するときなどに使用します。

**構文**

`beta_pdf(`*x*`, `*アルファ*`, `*ベータ*`)`

**引数**

* *x*: 関数を評価する値。
* *α*: 分布のパラメータ。
* *beta*: 分布のパラメータ。

**戻り値**

* [確率ベータ密度関数](https://en.wikipedia.org/wiki/Beta_distribution#Probability_density_function).

**メモ**

引数に数値以外の値を指定すると、beta_pdf() は null 値を返します。

x ≤ 0 または 1 ≤ x の場合、beta_pdf() は NaN 値を返します。

α ≦ 0 または β ≤ 0 の場合、beta_pdf() は NaN 値を返します。

**使用例**

```kusto
datatable(x:double, alpha:double, beta:double, comment:string)
[
    0.5, 10.0, 20.0, "Valid input",
    1.5, 10.0, 20.0, "x > 1, yields NaN",
    double(-10), 10.0, 20.0, "x < 0, yields NaN",
    0.1, double(-1.0), 20.0, "alpha is < 0, yields NaN"
]
| extend r = beta_pdf(x, alpha, beta)
```

|x|alpha|beta|comment|r|
|---|---|---|---|---|
|0.5|10|20|有効な入力|0.746176019310951|
|1.5|10|20|x > 1、収率 NaN|(NaN)|
|-10|10|20|x < 0、収率 NaN|(NaN)|
|0.1|-1|20|αは< 0、収率 NaN|(NaN)|

**参照**

* β累積確率密度関数の逆関数の計算については、 [beta-inv()](./beta-invfunction.md)を参照してください。
* 標準の累積ベータ分布関数については、 [beta-cdf()](./beta-cdffunction.md)を参照してください。