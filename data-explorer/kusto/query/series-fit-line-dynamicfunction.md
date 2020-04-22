---
title: series_fit_line_dynamic() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで series_fit_line_dynamic() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 03bbb143504d0540156d5f18d4ed527ab5d13d38
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663553"
---
# <a name="series_fit_line_dynamic"></a>series_fit_line_dynamic()

系列に線形回帰を適用し、動的オブジェクトを返します。  

入力として動的な数値配列を含む式を取り、それに最も適した線を見つけるために[線形回帰](https://en.wikipedia.org/wiki/Line_fitting)を実行します。 この関数は、make-series 演算子の出力に適合させるために、時系列配列で使用する必要があります。 次の内容の動的な値を生成します。
* `rsquare`: [r-square](https://en.wikipedia.org/wiki/Coefficient_of_determination)は、適合品質の標準尺度です。 これは [0-1] の範囲の数値です。1 は適合度が最も高いことを表し、0 はデータがまったく順序付けされておらず、どの直線にも適合しないことを意味します。 
* `slope`: 近似線の傾き (y= ax+b からの a)
* `variance`: 入力データの分散
* `rvariance`: 入力データ値の近似値の差を示す残差。
* `interception`: 近似線の回線のインターセプト (y=ax+b からの b)
* `line_fit`: 最適な適合線の一連の値を保持する数値配列。 系列の長さは入力配列の長さと同じです。 主にグラフ作成に使用します。

この演算子は[series_fit_line](series-fit-linefunction.md)に似ています`series-fit-line`が、動的バッグを返すのとは異なり、

**構文**

`series_fit_line_dynamic(`*X*`)`

**引数**

* *x*: 数値の動的配列。

> [!TIP]
> この関数を使用する最も便利な方法は、[系列の作成](make-seriesoperator.md)演算子の結果に適用することです。

**使用例**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend fit=series_fit_line_dynamic(y)
| extend RSquare=fit.rsquare, Slope=fit.slope, Variance=fit.variance,RVariance=fit.rvariance,Interception=fit.interception,LineFit=fit.line_fit
| render timechart
```

:::image type="content" source="images/samples/series-fit-line.png" alt-text="シリーズフィットライン":::

| rsquare | slope | Variance | rvariance | interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0.982   | 2.730 | 98.628   | 1.686     | -1.666       | 1.064, 3.7945, 6.526, 9.256, 11.987, 14.718, 17.449, 20.180, 22.910, 25.641, 28.371, 31.102 |