---
title: beta_pdf ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの beta_pdf () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6ef26e3a24d7bc6f1d4cd50f74ca83d0a1d21dab
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245482"
---
# <a name="beta_pdf"></a>beta_pdf()

確率密度のベータ関数を返します。

```kusto
beta_pdf(0.2, 10.0, 50.0)
```

通常、ベータ分布は複数の標本を対象として割合の変化を分析する場合などに使用します。たとえば、複数の人が 1 日のうちにテレビを見ている時間の割合を算出するときなどに使用します。

## <a name="syntax"></a>構文

`beta_pdf(`*x* `, `*アルファ* `, `*ベータ版*`)`

## <a name="arguments"></a>引数

* *x*: 関数を評価する値。
* *α*: 分布のパラメーター。
* *beta*: 分布のパラメーター。

## <a name="returns"></a>戻り値

* [確率のベータ密度関数](https://en.wikipedia.org/wiki/Beta_distribution#Probability_density_function)。

**ノート**

引数に数値以外の値を指定した場合、beta_pdf () は null 値を返します。

X ≤0または1≤ x の場合、beta_pdf () は NaN 値を返します。

Α≤0または beta ≤0の場合、beta_pdf () は NaN 値を返します。

## <a name="examples"></a>例

<!-- csl: https://help.kusto.windows.net/Samples -->
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

|x|alpha|ベータ|コメント|r|
|---|---|---|---|---|
|0.5|10|20|有効な入力|0.746176019310951|
|1.5|10|20|x > 1、NaN を生成します|NaN|
|-10|10|20|x < 0、NaN を生成します|NaN|
|0.1|-1|20|アルファは 0 <、NaN を生成します|NaN|

**参照**

* ベータ累積確率密度関数の逆の計算については、「 [beta-inv ()](./beta-invfunction.md)」を参照してください。
* 標準の累積ベータ配布関数については、「 [ベータ cdf ()](./beta-cdffunction.md)」を参照してください。
