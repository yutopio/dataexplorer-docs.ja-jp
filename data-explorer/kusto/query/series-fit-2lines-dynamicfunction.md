---
title: series_fit_2lines_dynamic() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで series_fit_2lines_dynamic() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: efb2743789c363a33e21ba472a945087b9b277cf
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663519"
---
# <a name="series_fit_2lines_dynamic"></a>series_fit_2lines_dynamic()

系列に 2 つのセグメントの線形回帰を適用し、動的オブジェクトを返します。  

動的な数値配列を含む式を入力として受け取り[、2 つのセグメントの線形回帰](https://en.wikipedia.org/wiki/Segmented_regression)を適用して、系列のトレンド変化を特定して定量化します。 この関数は、系列インデックスを反復処理し、各反復で系列を 2 つの部分に分割し、各部分に[(series_fit_line()](series-fit-linefunction.md)または[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)を使用して) 別々の行に収まり、合計 r-2 乗を計算します。 最良の分割は、r-square を最大化したものです。関数は、次の内容の動的な値でパラメーターを返します。
* `rsquare`: [r-square](https://en.wikipedia.org/wiki/Coefficient_of_determination)は、適合品質の標準尺度です。 これは [0-1] の範囲の数値です。1 は適合度が最も高いことを表し、0 はデータがまったく順序付けされておらず、どの直線にも適合しないことを意味します。
* `split_idx`: 2 セグメント (ゼロから始まる) に対するブレーク ポイントのインデックス
* `variance`: 入力データの分散
* `rvariance`: 入力データの差を(2線分で)近似値とする残差。
* `line_fit`: 最適な適合線の一連の値を保持する数値配列。 系列の長さは入力配列の長さと同じです。 主にグラフ作成に使用します。
* `right.rsquare`: 分割の右側の行の r-2 乗は[、series_fit_line()](series-fit-linefunction.md)または[series_fit_line_dynamic() を](series-fit-line-dynamicfunction.md)参照してください。
* `right.slope`: 右近似線の傾き (y=ax+b からの a)
* `right.interception`: 近似左行のインターセプト (y=ax+b からの b)
* `right.variance`: 分割の右側の入力データの分散
* `right.rvariance`: 分割の右側の入力データの残差分散
* `left.rsquare`: 分割の左側の行の r-2 乗は[、series_fit_line()](series-fit-linefunction.md)または[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)を参照してください。
* `left.slope`: 左近似線の傾き (y=ax+b からの a)
* `left.interception`: 近似左行のインターセプト (y=ax+b からの b)
* `left.variance`: 分割の左側の入力データの分散
* `left.rvariance`: 分割の左側の入力データの残差分散

この演算子は[series_fit_2lines](series-fit-2linesfunction.md)に似ています`series-fit-2lines`が、動的バッグを返すのとは異なり、

**構文**

`series_fit_2lines_dynamic(`*X*`)`

**引数**

* *x*: 数値の動的配列。  

> [!TIP]
> この関数を使用する最も便利な方法は、[系列の作成](make-seriesoperator.md)演算子の結果に適用することです。

**使用例**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend LineFit=series_fit_line_dynamic(y).line_fit, LineFit2=series_fit_2lines_dynamic(y).line_fit
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/samples/series-fit-2lines.png" alt-text="シリーズフィット2行":::
