---
title: series_fit_line_dynamic ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの series_fit_line_dynamic () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 170348dd530b581f85e0323563be324d5b795511
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618719"
---
# <a name="series_fit_line_dynamic"></a>series_fit_line_dynamic()

系列に線形回帰を適用し、動的オブジェクトを返します。  

動的な数値配列を含む式を入力として受け取り、最適な行を見つけるために[線形回帰](https://en.wikipedia.org/wiki/Line_fitting)を実行します。 この関数は、make-series 演算子の出力に適合させるために、時系列配列で使用する必要があります。 次の内容で動的な値が生成されます。
* `rsquare`: [r-2 乗](https://en.wikipedia.org/wiki/Coefficient_of_determination)は、適合品質の標準メジャーです。 これは [0-1] の範囲の数値です。1 は適合度が最も高いことを表し、0 はデータがまったく順序付けされておらず、どの直線にも適合しないことを意味します。 
* `slope`: 近似直線の傾き (y = ax + b の a)
* `variance`: 入力データの分散
* `rvariance`: 入力データ値と近似された値との間の分散である残余の差異。
* `interception`: 近似直線のインターセプト (y = ax + b の b)
* `line_fit`: 最適な行の一連の値を保持する数値配列。 系列の長さは入力配列の長さと同じです。 主にグラフ作成に使用します。

この演算子は[series_fit_line](series-fit-linefunction.md)と似`series-fit-line`ていますが、動的なバッグを返します。

**構文**

`series_fit_line_dynamic(`*閉じる*`)`

**引数**

* *x*: 数値の動的配列。

> [!TIP]
> この関数を使用する最も便利な方法は、これを[系列](make-seriesoperator.md)演算子の結果に適用することです。

**使用例**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend fit=series_fit_line_dynamic(y)
| extend RSquare=fit.rsquare, Slope=fit.slope, Variance=fit.variance,RVariance=fit.rvariance,Interception=fit.interception,LineFit=fit.line_fit
| render timechart
```

:::image type="content" source="images/series-fit-line/series-fit-line.png" alt-text="系列の線を合わせる":::

| rsquare | slope | Variance | rvariance | interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0.982   | 2.730 | 98.628   | 1.686     | -1.666       | 1.064、3.7945、6.526、9.256、11.987、14.718、17.449、20.180、22.910、25.641、28.371、31.102 |