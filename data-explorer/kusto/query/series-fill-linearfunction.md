---
title: series_fill_linear() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの series_fill_linear() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4f9a12d172a1580d6a9e575b78b14404dce7820e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508655"
---
# <a name="series_fill_linear"></a>series_fill_linear()

系列内の欠損値の線形補間を実行します。

動的な数値配列を含む式を入力として受け取り、missing_value_placeholderのすべてのインスタンスに対して線形補間を実行し、結果の配列を返します。 配列の先頭と末尾にmissing_value_placeholderが含まれている場合、missing_value_placeholder以外の最も近い値に置き換えられます (オフにできます)。 配列全体がmissing_value_placeholderで構成されている場合、配列はconstant_valueで埋められます。  

**構文**

`series_fill_linear(`*x* `[,` *missing_value_placeholder*` [,`*fill_edges*` [,`*constant_value*`]]]))`
* 指定したパラメータを使用して *、x*の系列線形補間を返します。
 

**引数**

* *x*: 数値の配列である動的配列スカラー式。
* *missing_value_placeholder*: 置換する "欠損値" のプレースホルダーを指定するオプションのパラメーターです。 デフォルト値は`double`(*null*) です。
* *fill_edges*: 配列の先頭と末尾の*missing_value_placeholder*を最も近い値に置き換えるかどうかを示すブール値です。 デフォルトでは*true です*。 *false*に設定すると、配列の先頭と末尾の*missing_value_placeholder*が保持されます。
* *constant_value*: 配列にのみ関連するオプションのパラメータは、完全に*null*値で構成され、系列に一致する定数値を指定します。 既定値は*0*です。 このパラメーターを ( `double`*null*) に設定すると、*事実上、NULL*値がそのまま残ります。

**メモ**

* [make-series](make-seriesoperator.md)の後に補間関数を適用するためには、デフォルト値として*null*を指定することをお勧めします。 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* *missing_value_placeholder*は、実際の要素型に変換される任意の型を指定できます。 `double`したがって、 ( `long`*null*) `int`(*null*) または (*null*) は同じ意味を持ちます。
* *missing_value_placeholder*が`double`(*null*) (または同じ意味を持つ単に省略された) 場合、結果には*NULL*値が含まれる可能性があります。 他の補間関数を使用して、それらを埋めます。 現在[、series_outliers()](series-outliersfunction.md)のみが入力配列で*null 値*をサポートしています。
* この関数は、配列要素の元の型を保持します。 *x*に*int*要素または*long*要素のみが含まれている場合、線形補間は正確な値ではなく、丸められた補間値を返します。

**例**

```kusto
let data = datatable(arr: dynamic)
[
    dynamic([null, 111.0, null, 36.0, 41.0, null, null, 16.0, 61.0, 33.0, null, null]), // Array of double    
    dynamic([null, 111,   null, 36,   41,   null, null, 16,   61,   33,   null, null]), // Similar array of int
    dynamic([null, null, null, null])                                                   // Array with missing values only
];
data
| project arr, 
          without_args = series_fill_linear(arr),
          with_edges = series_fill_linear(arr, double(null), true),
          wo_edges = series_fill_linear(arr, double(null), false),
          with_const = series_fill_linear(arr, double(null), true, 3.14159)  

```

|Arr|without_args|with_edges|wo_edges|with_const|
|---|---|---|---|---|
|[null,111.0,null,36.0,41.0,null,null,16.0,61.0,33.0,null,null]|[111.0,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,33.0,33.0]|[111.0,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,33.0,33.0]|[null,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,null,null]|[111.0,111.0,73.5,36.0,41.0,32.667,24.333,16.0,61.0,33.0,33.0,33.0]|
|[null,111,null,36,41,null,null,16,61,33,null,null]|[111,111,73,36,41,32,24,16,61,33,33,33]|[111,111,73,36,41,32,24,16,61,33,33,33]|[null,111,73,36,41,32,24,16,61,33,null,null]|[111,111,74,38, 41,32,24,16,61,33,33,33]|
|[null、null、null、null]|[0.0,0.0,0.0,0.0]|[0.0,0.0,0.0,0.0]|[0.0,0.0,0.0,0.0]|[3.14159,3.14159,3.14159,3.14159]|