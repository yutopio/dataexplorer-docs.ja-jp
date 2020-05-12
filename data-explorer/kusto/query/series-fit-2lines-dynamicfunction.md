---
title: series_fit_2lines_dynamic ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_fit_2lines_dynamic () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 43eab4e8e5ee47fd8c3f7d63d562af20ceb7b249
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226603"
---
# <a name="series_fit_2lines_dynamic"></a>series_fit_2lines_dynamic()

系列に2つのセグメントの線形回帰を適用し、動的オブジェクトを返します。  

動的な数値配列を含む式を入力として受け取り、 [2 つのセグメントの線形回帰](https://en.wikipedia.org/wiki/Segmented_regression)を適用して、系列の傾向の変化を特定し、定量化します。 関数は、系列インデックスに対して反復処理を行います。 各イテレーションでは、系列が2つの部分に分割され、 [series_fit_line ()](series-fit-linefunction.md)または[series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md)を使用して個別の行に配置されます。 関数は、2つの部分のそれぞれに線を合わせ、R-2 乗値の合計を計算します。 最適な分割は、R-2 乗を最大化したものです。 関数は、次の内容を使用して動的な値でパラメーターを返します。

* `rsquare`: [R-2 乗](https://en.wikipedia.org/wiki/Coefficient_of_determination)は、適合品質の標準メジャーです。 これは [0-1] の範囲の数値であり、1は最適な適合であり、0はデータが順序付けられておらず、どの行にも適合しないことを意味します。
* `split_idx`: 2 つのセグメントへのブレークポイントのインデックス (0 から始まる)。
* `variance`: 入力データの分散。
* `rvariance`: 入力データ値と近似されるデータ値の間の分散 (2 つの線分による)。
* `line_fit`: 最適な行の一連の値を保持する数値配列。 系列の長さは入力配列の長さと同じです。 これは、グラフ作成に使用されます。
* `right.rsquare`: 分割の右側の線の r-2 乗は、「 [series_fit_line ()](series-fit-linefunction.md) 」または「 [series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md)」を参照してください。
* `right.slope`: 右の近似直線の傾き (y = ax + b の形式)。
* `right.interception`: 近似左側の行のインターセプト (b from y = ax + b)。
* `right.variance`: 分割の右側の入力データの分散。
* `right.rvariance`: 分割の右側の入力データの残差分散。
* `left.rsquare`: 分割の左側の行の r-2 乗、[series_fit_line ()] を参照してください。(series-fit-linefunction.md) または[series_fit_line_dynamic ()](series-fit-line-dynamicfunction.md)。
* `left.slope`: 左の近似直線の傾き (y = ax + b の形式)。
* `left.interception`: 近似左側の行のインターセプト (y = ax + b の形式)。
* `left.variance`: 分割の左側の入力データの分散。
* `left.rvariance`: 分割の左側の入力データの残差分散。

この演算子は[series_fit_2lines](series-fit-2linesfunction.md)に似ています。 とは異なり `series-fit-2lines` 、動的なバッグを返します。

**構文**

`series_fit_2lines_dynamic(`*閉じる*`)`

**引数**

* *x*: 数値の動的配列。  

> [!TIP]
> この関数を使用する最も便利な方法は、[系列の作成](make-seriesoperator.md)演算子の結果に適用することです。

**例**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend LineFit=series_fit_line_dynamic(y).line_fit, LineFit2=series_fit_2lines_dynamic(y).line_fit
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/series-fit-2lines/series-fit-2lines.png" alt-text="2行に一致する系列":::
