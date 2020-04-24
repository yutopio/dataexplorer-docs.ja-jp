---
title: beta_inv() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでbeta_inv() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 20bdf8bfc01ef3ac6c6a12f6a43d87fd7b5c07e6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517886"
---
# <a name="beta_inv"></a>beta_inv()

ベータ累積確率 β 密度関数の逆関数を返します。

```kusto
beta_inv(0.1, 10.0, 50.0)
```

*probability* = 確率`beta_cdf(`*x が*,...`)`を選択`beta_inv(`し、*確率*,...`)` *.*  =  

ベータ分布は、プロジェクト計画などで、期待される完了時間とばらつきを指定して予想完了時間をモデル化する場合に使用できます。

**構文**

`beta_inv(`*確率*`, `*alpha*アルファ`, `*ベータ*`)`

**引数**

* *確率*: ベータ分布に関連する確率。
* *α*: 分布のパラメータ。
* *beta*: 分布のパラメータ。

**戻り値**

* β累積確率密度関数[beta_cdf()](./beta-cdffunction.md)の逆数

**メモ**

引数に数値以外の値を指定すると、beta_inv() は null 値を返します。

α ≦ 0 または β ≤ 0 の場合、beta_inv() は null 値を返します。

確率 ≤ 0 または確率 > 1 の場合、beta_inv() は NaN 値を返します。

確率の値を指定すると、beta_inv()は、beta_cdf(x、α、β)=確率のように、その値xを求めます。

**使用例**

```kusto
datatable(p:double, alpha:double, beta:double, comment:string)
[
    0.1, 10.0, 20.0, "Valid input",
    1.5, 10.0, 20.0, "p > 1, yields null",
    0.1, double(-1.0), 20.0, "alpha is < 0, yields NaN"
]
| extend b = beta_inv(p, alpha, beta)
```

|p|alpha|beta|comment|b|
|---|---|---|---|---|
|0.1|10|20|有効な入力|0.226415022388749|
|1.5|10|20|p > 1、null を返す||
|0.1|-1|20|αは< 0、収率 NaN|(NaN)|

**参照**

* 累積ベータ分布関数の計算については、 [beta-cdf()](./beta-cdffunction.md)を参照してください。
* 確率β密度関数の計算については、 [β-pdf()](./beta-pdffunction.md)を参照してください。