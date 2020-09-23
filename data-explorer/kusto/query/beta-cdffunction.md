---
title: beta_cdf ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの beta_cdf () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b500f5f0e727fde315bea8d77ab60f600f127271
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103403"
---
# <a name="beta_cdf"></a>beta_cdf()

標準の累積ベータ分布関数を返します。

```kusto
beta_cdf(0.2, 10.0, 50.0)
```

*確率*  =  `beta_cdf(` *x*,... の場合は `)` 、 `beta_inv(` *確率*,... `)`  = *x*。

通常、ベータ分布は複数の標本を対象として割合の変化を分析する場合などに使用します。たとえば、複数の人が 1 日のうちにテレビを見ている時間の割合を算出するときなどに使用します。

## <a name="syntax"></a>構文

`beta_cdf(`*x* `, `*アルファ* `, `*ベータ版*`)`

## <a name="arguments"></a>引数

* *x*: 関数を評価する値。
* *α*: 分布のパラメーター。
* *beta*: 分布のパラメーター。

## <a name="returns"></a>戻り値

* [累積ベータ分布関数](https://en.wikipedia.org/wiki/Beta_distribution#Cumulative_distribution_function)です。

**メモ**

引数に数値以外の値を指定した場合、beta_cdf () は null 値を返します。

X < 0 または x > 1 の場合、beta_cdf () は NaN 値を返します。

Α≤0または alpha > 1万の場合、beta_cdf () は NaN 値を返します。

Beta ≤0または beta > 1万の場合、beta_cdf () は NaN 値を返します。

## <a name="examples"></a>例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(x:double, alpha:double, beta:double, comment:string)
[
    0.9, 10.0, 20.0, "Valid input",
    1.5, 10.0, 20.0, "x > 1, yields NaN",
    double(-10), 10.0, 20.0, "x < 0, yields NaN",
    0.1, double(-1.0), 20.0, "alpha is < 0, yields NaN"
]
| extend b = beta_cdf(x, alpha, beta)
```

|x|alpha|ベータ|コメント|b|
|---|---|---|---|---|
|0.9|10|20|有効な入力|0.999999999999959|
|1.5|10|20|x > 1、NaN を生成します|NaN|
|-10|10|20|x < 0、NaN を生成します|NaN|
|0.1|-1|20|アルファは 0 <、NaN を生成します|NaN|


## <a name="see-also"></a>関連項目


* ベータ累積確率密度関数の逆の計算については、「 [beta-inv ()](./beta-invfunction.md)」を参照してください。
* 計算確率密度関数については、「 [beta-pdf ()](./beta-pdffunction.md)」を参照してください。
