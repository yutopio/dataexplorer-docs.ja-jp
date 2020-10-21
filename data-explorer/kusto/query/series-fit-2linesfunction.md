---
title: series_fit_2lines ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_fit_2lines () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: f056a14d7f1a88a7f4b77e01920ae2f00ae4bc45
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242111"
---
# <a name="series_fit_2lines"></a>series_fit_2lines()

系列に2つのセグメントの線形回帰を適用し、複数の列を返します。  

動的な数値配列を含む式を入力として受け取り、 [2 つのセグメントの線形回帰](https://en.wikipedia.org/wiki/Segmented_regression) を適用して、系列の傾向の変化を識別および定量化します。 関数は、系列インデックスに対して反復処理を行います。 各反復処理では、関数によって系列が2つの部分に分割され、各部分に対して ( [series_fit_line ()](series-fit-linefunction.md)を使用して) 個別の行が配置され、合計の r-2 乗が計算されます。 最適な分割は r-2 乗を最大化したものです。この関数は次のパラメーターを返します。


|パラメーター  |説明  |
|---------|---------|
|`rsquare`     | [R-2 乗](https://en.wikipedia.org/wiki/Coefficient_of_determination) は、適合品質の標準測定値です。 これは [0-1] の範囲の数値であり、1-は最適な適合で、0はデータが順序付けられておらず、どの行にも適合しないことを意味します。        |
|`split_idx`     |   2つのセグメントのブレークポイントのインデックス (0 から始まる)。      |
|`variance`     | 入力データの分散。        |
|`rvariance`     | 残余差異。これは、入力データ値が近似された値 (2 つの線分によって) の間の分散です。        |
|`line_fit`     | 最適な線の一連の値を保持する数値配列。 系列の長さは入力配列の長さと同じです。 これは主にグラフ作成に使用されます。        |
|`right_rsquare`     | 分割の右側にある直線の r-2 乗「 [series_fit_line ()](series-fit-linefunction.md)」を参照してください。        |
|`right_slope`     | 近似直線の傾き (y = ax + b の形式)。         |
|`right_interception`     |  近似された左側の行のインターセプト (b from y = ax + b)。       |
|`right_variance`    | 分割の右側の入力データの分散。        |
|`right_rvariance`     | 分割の右側の入力データの残差分散。        |
|`left_rsquare`     | 分割の左側の行の r-2 乗「 [series_fit_line ()](series-fit-linefunction.md)」を参照してください。        |
|`left_slope`    | 左の近似直線の傾き (y = ax + b の形式)。        |
|`left_interception`     |   近似左側の行のインターセプト (y = ax + b の形式)。      |
|`left_variance`     | 分割の左側の入力データの分散。        |
|`left_rvariance`     | 分割の左側の入力データの残余差異。        |


> [!Note]
> この関数は複数の列を返します。そのため、別の関数の引数として使用することはできません。

## <a name="syntax"></a>構文

プロジェクト `series_fit_2lines(` *x*`)`
* では、上記のすべての列が返されます。名前は series_fit_2lines_x_rsquare、series_fit_2lines_x_split_idx などです。

プロジェクト (rs、si、v) = `series_fit_2lines(` *x*`)`
* は、rs (r-2 乗)、si (split index)、v (分散) の各列を返します。残りの列は series_fit_2lines_x_rvariance、series_fit_2lines_x_line_fit などになります。

拡張 (rs、si、v) = `series_fit_2lines(` *x*`)`
* rs (r-2 乗)、si (分割インデックス)、v (差異) のみを返します。
  
## <a name="arguments"></a>引数

* *x*: 数値の動的配列。  

> [!TIP]
> この関数を使用する最も便利な方法は、これを [系列](make-seriesoperator.md) 演算子の結果に適用することです。

## <a name="examples"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(y), (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(y)
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/series-fit-2lines/series-fit-2lines.png" alt-text="2行に一致する系列":::
