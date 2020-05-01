---
title: series_fit_2lines_dynamic ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの series_fit_2lines_dynamic () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: e2671b112ff1a73a5f9bbc10d6537a5e8d7b755e
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618770"
---
# <a name="series_fit_2lines_dynamic"></a>series_fit_2lines_dynamic()

系列に2つのセグメントの線形回帰を適用し、動的オブジェクトを返します。  

動的な数値配列を含む式を入力として受け取り、 [2 つのセグメントの線形回帰](https://en.wikipedia.org/wiki/Segmented_regression)を適用して、系列の傾向の変化を特定し、定量化します。 関数は、系列インデックスに対して反復処理を行い、各反復処理で系列を2つの部分に分割し、各部分に対して ( [series_fit_line ()](series-fit-linefunction.md)または[series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md)を使用して) 1 つの行を分割し、合計 r-2 乗を計算します。 最適な分割は、r-2 乗を最大化したものです。関数は、次の内容を使用して動的な値でパラメーターを返します。
* `rsquare`: [r-2 乗](https://en.wikipedia.org/wiki/Coefficient_of_determination)は、適合品質の標準メジャーです。 これは [0-1] の範囲の数値です。1 は適合度が最も高いことを表し、0 はデータがまったく順序付けされておらず、どの直線にも適合しないことを意味します。
* `split_idx`: 2 つのセグメントへのブレークポイントのインデックス (0 から始まる)
* `variance`: 入力データの分散
* `rvariance`: (2 つの線分によって) 近似された入力データ値の間の分散である残留差異。
* `line_fit`: 最適な行の一連の値を保持する数値配列。 系列の長さは入力配列の長さと同じです。 主にグラフ作成に使用します。
* `right.rsquare`: 分割の右側の線の r-2 乗は、「 [series_fit_line ()](series-fit-linefunction.md) 」または「[series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md) 」を参照してください。
* `right.slope`: 右の近似直線の傾き (y = ax + b の a)
* `right.interception`: 近似左側の行のインターセプト (y = ax + b の b)
* `right.variance`: 分割の右側の入力データの分散
* `right.rvariance`: 分割の右側の入力データの残差分散
* `left.rsquare`: 分割の左側の行の r-2 乗は、「 [series_fit_line ()](series-fit-linefunction.md) 」または「 [series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md) 」を参照してください。
* `left.slope`: 左の近似直線の傾き (y = ax + b の a)
* `left.interception`: 近似左側の行のインターセプト (y = ax + b の b)
* `left.variance`: 分割の左側の入力データの分散
* `left.rvariance`: 分割の左側の入力データの残差分散

この演算子は[series_fit_2lines](series-fit-2linesfunction.md)と似`series-fit-2lines`ていますが、動的なバッグを返します。

**構文**

`series_fit_2lines_dynamic(`*閉じる*`)`

**引数**

* *x*: 数値の動的配列。  

> [!TIP]
> この関数を使用する最も便利な方法は、これを[系列](make-seriesoperator.md)演算子の結果に適用することです。

**使用例**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend LineFit=series_fit_line_dynamic(y).line_fit, LineFit2=series_fit_2lines_dynamic(y).line_fit
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/series-fit-2lines/series-fit-2lines.png" alt-text="2行に一致する系列":::
