---
title: series_fit_poly ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_fit_poly () について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/21/2020
ms.openlocfilehash: 68f9f55b1ff0c66bb5d50e624f9063d7e177f50a
ms.sourcegitcommit: d0f8d71261f8f01e7676abc77283f87fc450c7b1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2020
ms.locfileid: "91771820"
---
# <a name="series_fit_poly"></a>series_fit_poly ()

独立変数 (x_series) から依存変数 (y_series) への多項式回帰を適用します。 この関数は、複数の系列 (動的な数値配列) を含むテーブルを取得し、 [多項式回帰](https://en.wikipedia.org/wiki/Polynomial_regression)を使用して各系列に最適な順序の多項式を生成します。 

> [!TIP]
> * 系列 [演算子](make-seriesoperator.md)によって作成された等間隔の系列の線形回帰では、より単純な関数 [series_fit_line ()](series-fit-linefunction.md)を使用します。 [例 2](#example-2)を参照してください。
> * *X_series*が指定されていて、回帰が高レベルで実行されている場合は、[0-1] の範囲に正規化することを検討してください。 [例 3](#example-3)を参照してください。
> * *X_series*が datetime 型の場合は、double 型に変換され、正規化される必要があります。 [例 3](#example-3)を参照してください。
> * インライン Python を使用した多項式回帰の参照実装については、「 [series_fit_poly_fl ()](../functions-library/series-fit-poly-fl.md)」を参照してください。


## <a name="syntax"></a>構文

`T | extend  series_fit_poly(`*y_series* `, `*x_series* `, `*度*`)`
  
## <a name="arguments"></a>引数

|引数| 説明| 必須/省略可能| Notes|
|---|---|---|---|
| *y_series* | [依存変数](https://en.wikipedia.org/wiki/Dependent_and_independent_variables)を格納している動的な数値配列。 | 必須 |
| *x_series* | [独立変数](https://en.wikipedia.org/wiki/Dependent_and_independent_variables)を含む動的な数値配列。 | 省略可能。 [均等の連続系列](https://en.wikipedia.org/wiki/Unevenly_spaced_time_series)にのみ必要です。 | 指定しない場合、既定値の [1, 2,..., length (y_series)] に設定されます。|
| *どの* | 多項式を収めるために必要な順序。 たとえば、線形回帰の場合は1、2次回帰の場合は2になります。 | 省略可能 | 既定値は 1 (線形回帰) です。|

## <a name="returns"></a>戻り値

関数は、 `series_fit_poly()` 次の列を返します。

* `rsquare`: [r-2 乗](https://en.wikipedia.org/wiki/Coefficient_of_determination) は、適合品質の標準メジャーです。 値は [0-1] の範囲の数値です。 1-は最適な適合で、0はデータが順序付けられておらず、どの線にも適合しないことを意味します。
* `coefficients`: 指定された次数で最適な多項式の係数を保持する数値配列。最大のべき乗係数から最低値までの順序で並べ替えられます。
* `variance`: 依存変数 (y_series) の分散。
* `rvariance`: 入力データ値と近似された値との間の差異である残余の差異。
* `poly_fit`: 最適な多項式の一連の値を保持する数値配列。 系列の長さは、依存変数 (y_series) の長さと同じです。 グラフ作成に使用される値。

## <a name="examples"></a>例

### <a name="example-1"></a>例 1

X & y 軸でのノイズを含む5番目の注文多項式:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 200 step 1
| project x = rand()*5 - 2.3
| extend y = pow(x, 5)-8*pow(x, 3)+10*x+6
| extend y = y + (rand() - 0.5)*0.5*y
| summarize x=make_list(x), y=make_list(y)
| extend series_fit_poly(y, x, 5)
| project-rename fy=series_fit_poly_y_poly_fit, coeff=series_fit_poly_y_coefficients
|fork (project x, y, fy) (project-away x, y, fy)
| render linechart 
```

:::image type="content" source="images/series-fit-poly-function/fifth-order-noise-1.png" alt-text="5番目の注文多項式がノイズのあるシリーズに均等に表示されるグラフ":::

:::image type="content" source="images/series-fit-poly-function/fifth-order-noise-table-1.png" alt-text="5番目の注文多項式がノイズのあるシリーズに均等に表示されるグラフ" border="false":::

### <a name="example-2"></a>例 2

度 = 1 の一致があることを確認し `series_fit_poly` `series_fit_line` ます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_series1
| extend series_fit_line(y)
| extend series_fit_poly(y)
| project-rename y_line = series_fit_line_y_line_fit, y_poly = series_fit_poly_y_poly_fit
| fork (project x, y, y_line, y_poly) (project-away id, x, y, y_line, y_poly) 
| render linechart with(xcolumn=x, ycolumns=y, y_line, y_poly)
```

:::image type="content" source="images/series-fit-poly-function/fit-poly-line.png" alt-text="5番目の注文多項式がノイズのあるシリーズに均等に表示されるグラフ":::

:::image type="content" source="images/series-fit-poly-function/fit-poly-line-table.png" alt-text="5番目の注文多項式がノイズのあるシリーズに均等に表示されるグラフ" border="false":::
    
### <a name="example-3"></a>例 3

不規則 (均等間隔) の時系列:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  x-axis must be normalized to the range [0-1] if either degree is relatively big (>= 5) or original x range is big.
//  so if x is a time axis it must be normalized as conversion of timestamp to long generate huge numbers (number of 100 nano-sec ticks from 1/1/1970)
//
//  Normalization: x_norm = (x - min(x))/(max(x) - min(x))
//
irregular_ts
| extend series_stats(series_add(TimeStamp, 0))                                                                 //  extract min/max of time axis as doubles
| extend x = series_divide(series_subtract(TimeStamp, series_stats__min), series_stats__max-series_stats__min)  // normalize time axis to [0-1] range
| extend series_fit_poly(num, x, 8)
| project-rename fnum=series_fit_poly_num_poly_fit
| render timechart with(ycolumns=num, fnum)
```
:::image type="content" source="images/series-fit-poly-function/irregular-time-series-1.png" alt-text="5番目の注文多項式がノイズのあるシリーズに均等に表示されるグラフ":::
