---
title: series_fit_line ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_fit_line () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 9731e3384fb0109c37ad6c0ca262a954ef5dd470
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248454"
---
# <a name="series_fit_line"></a>series_fit_line()

系列に線形回帰を適用し、複数の列を返します。  

動的な数値配列を含む式を入力として受け取り、 [線形回帰](https://en.wikipedia.org/wiki/Line_fitting) を実行して最適な行を見つけます。 この関数は、make-series 演算子の出力に適合させるために、時系列配列で使用する必要があります。 関数は、次の列を生成します。
* `rsquare`: [r-2 乗](https://en.wikipedia.org/wiki/Coefficient_of_determination) は、適合品質の標準メジャーです。 値は [0-1] の範囲の数値です。 1-は最適な適合で、0はデータが順序付けられておらず、どの線にも適合しないことを意味します。 
* `slope`: 近似直線の傾き (y = ax + b の "a")。
* `variance`: 入力データの分散。
* `rvariance`: 入力データ値と近似された値との間の差異である残余の差異。
* `interception`: 近似直線のインターセプト (y = ax + b の "b")。
* `line_fit`: 最適な行の一連の値を保持する数値配列。 系列の長さは入力配列の長さと同じです。 グラフ作成に使用される値。

## <a name="syntax"></a>構文

`series_fit_line(`*閉じる*`)`

## <a name="arguments"></a>引数

* *x*: 数値の動的配列。

> [!TIP]
> この関数を使用する最も便利な方法は、 [系列の作成](make-seriesoperator.md) 演算子の結果に適用することです。

## <a name="examples"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(y)
| render timechart
```

:::image type="content" source="images/series-fit-line/series-fit-line.png" alt-text="系列の線を合わせる":::

| rsquare | 傾き | Variance | rvariance | interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0.982   | 2.730 | 98.628   | 1.686     | -1.666       | 1.064、3.7945、6.526、9.256、11.987、14.718、17.449、20.180、22.910、25.641、28.371、31.102 |
