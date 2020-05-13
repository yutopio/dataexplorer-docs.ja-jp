---
title: series_fit_2lines ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_fit_2lines () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d4b4be37f171439b47399ecfbb314b1a9b704afd
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372707"
---
# <a name="series_fit_2lines"></a>series_fit_2lines()

系列に2つのセグメントの線形回帰を適用し、複数の列を返します。  

動的な数値配列を含む式を入力として受け取り、 [2 つのセグメントの線形回帰](https://en.wikipedia.org/wiki/Segmented_regression)を適用して、系列の傾向の変化を特定し、定量化します。 関数は、系列インデックスに対して反復処理を行い、各反復処理で系列を2つの部分に分割し、各部分に対して ( [series_fit_line ()](series-fit-linefunction.md)を使用して) 個々の行に一致させることにより、合計 r-2 乗を計算します。 最適な分割は r-2 乗を最大化したものです。この関数は次のパラメーターを返します。
* `rsquare`: [r-2 乗](https://en.wikipedia.org/wiki/Coefficient_of_determination)は、適合品質の標準メジャーです。 これは [0-1] の範囲の数値です。1 は適合度が最も高いことを表し、0 はデータがまったく順序付けされておらず、どの直線にも適合しないことを意味します。
* `split_idx`: 2 つのセグメントへのブレークポイントのインデックス (0 から始まる)
* `variance`: 入力データの分散
* `rvariance`: (2 つの線分によって) 近似された入力データ値の間の分散である残留差異。
* `line_fit`: 最適な行の一連の値を保持する数値配列。 系列の長さは入力配列の長さと同じです。 主にグラフ作成に使用します。
* `right_rsquare`: 分割の右側にある直線の r-2 乗、「 [series_fit_line ()](series-fit-linefunction.md) 」を参照してください。
* `right_slope`: 右の近似直線の傾き (y = ax + b の a)
* `right_interception`: 近似左側の行のインターセプト (y = ax + b の b)
* `right_variance`: 分割の右側の入力データの分散
* `right_rvariance`: 分割の右側の入力データの残差分散
* `left_rsquare`: 分割の左側の行の r-2 乗、「 [series_fit_line ()](series-fit-linefunction.md) 」を参照してください。
* `left_slope`: 左の近似直線の傾き (y = ax + b の a)
* `left_interception`: 近似左側の行のインターセプト (y = ax + b の b)
* `left_variance`: 分割の左側の入力データの分散
* `left_rvariance`: 分割の左側の入力データの残差分散

この関数は複数の列を返すので、別の関数の引数としては使用できないことに*注意*してください。

**構文**

プロジェクト `series_fit_2lines(` *x*`)`
* は、前述のすべての列を返します (series_fit_2lines_x_rsquare、series_fit_2lines_x_split_idx など)。
プロジェクト (rs、si、v) = `series_fit_2lines(` *x*`)`
* は、次の列を返します。 rs (r-2 乗)、si (split index)、v (分散)、およびその他の列は series_fit_2lines_x_rvariance、series_fit_2lines_x_line_fit などのようになります。拡張 (rs, si, v) = `series_fit_2lines(` *x*`)`
* rs (r-2 乗)、si (分割インデックス)、v (差異) のみを返します。
  
**引数**

* *x*: 数値の動的配列。  

> [!TIP]
> この関数を使用する最も便利な方法は、これを[系列](make-seriesoperator.md)演算子の結果に適用することです。

**使用例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(y), (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(y)
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/series-fit-2lines/series-fit-2lines.png" alt-text="2行に一致する系列":::
