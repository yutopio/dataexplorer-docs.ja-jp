---
title: series_fill_linear ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_fill_linear () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4cec053990457a6b33c7446c5b32c63713320de9
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372760"
---
# <a name="series_fill_linear"></a>series_fill_linear()

系列内の欠損値を線形補間します。

動的な数値配列を含む式を入力として受け取り、missing_value_placeholder のすべてのインスタンスに対して線形補間を実行し、結果の配列を返します。 配列の先頭と末尾に missing_value_placeholder が含まれている場合は、missing_value_placeholder 以外の最も近い値に置き換えられます。 この機能は無効にすることができます。 配列全体が missing_value_placeholder で構成されている場合は、配列が constant_value で格納されます。指定されていない場合は0になります。  

**構文**

`series_fill_linear(`*x* `[,` *missing_value_placeholder* ` [,` *fill_edges* ` [,` *constant_value*`]]]))`
* 指定されたパラメーターを使用して*x*の系列線形補間を返します。
 

**引数**

* *x*: 数値の配列である動的配列スカラー式。
* *missing_value_placeholder*: 省略可能なパラメーター。置き換えられる "欠損値" のプレースホルダーを指定します。 既定値は `double` (*null*) です。
* *fill_edges*: 配列の先頭と末尾の*missing_value_placeholder*を最も近い値に置き換えるかどうかを示すブール値。 既定では*True*です。 *False*に設定すると、配列の先頭と末尾に*missing_value_placeholder*が保持されます。
* *constant_value*: 配列だけに関連する省略可能なパラメーターは、すべて*null*値で構成されます。 このパラメーターは、系列に値を格納する定数値を指定します。 既定値は*0*です。 このパラメーターを `double` (*null*) に設定すると、事実上*null*値が保持されます。

**メモ**

* [作成系列](make-seriesoperator.md)の後に補間関数を適用するには、既定値として*null*を指定します。 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* *Missing_value_placeholder*は、実際の要素型に変換される任意の型にすることができます。 そのため、 `double` (*null*)、 `long` (*null*)、 `int` (*null*) は同じ意味を持ちます。
* *Missing_value_placeholder*が `double` (*null*) の場合 (または同じ意味を持つ省略した場合)、結果に*null*値が含まれる可能性があります。 これらの*null*値を入力するには、他の補間関数を使用します。 現時点では、 [series_outliers ()](series-outliersfunction.md)のみが入力配列で*null*値をサポートしています。
* 関数は、元の型の配列要素を保持します。 X に int 要素または long 要素のみが含まれている場合、線形補間は、正確な補間ではなく、丸められた補間値を返します。

**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

|`arr`|`without_args`|`with_edges`|`wo_edges`|`with_const`|
|---|---|---|---|---|
|[null、111.0、null、36.0、41.0、null、null、16.0、61.0、33.0、null、null]|[111.0、111.0、73.5、36.0、41.0、32.667、24.333、16.0、61.0、33.0、33.0、33.0]|[111.0、111.0、73.5、36.0、41.0、32.667、24.333、16.0、61.0、33.0、33.0、33.0]|[null、111.0、73.5、36.0、41.0、32.667、24.333、16.0、61.0、33.0、null、null]|[111.0、111.0、73.5、36.0、41.0、32.667、24.333、16.0、61.0、33.0、33.0、33.0]|
|[null、111、null、36、41、null、null、16、61、33、null、null]|[111111、73、36、41、32、24、16、61、33、33、33]|[111111、73、36、41、32、24、16、61、33、33、33]|[null、111、73、36、41、32、24、16、61、33、null、null]|[111111、74、38、41、32、24、16、61、33、33、33]|
|[null、null、null、null]|[0.0、0.0、0.0、0.0]|[0.0、0.0、0.0、0.0]|[0.0、0.0、0.0、0.0]|[3.14159、3.14159、3.14159、3.14159]|
