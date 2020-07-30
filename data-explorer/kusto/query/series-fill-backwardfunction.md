---
title: series_fill_backward ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_fill_backward () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 42012111c7f862672a1335e0e9165eb62179dfe7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87344928"
---
# <a name="series_fill_backward"></a>series_fill_backward()

系列内の欠損値の後方塗りつぶし補間を実行します。

動的な数値配列を含む式は入力です。 関数は、missing_value_placeholder のすべてのインスタンスを右端 (missing_value_placeholder 以外) の最も近い値に置き換え、結果の配列を返します。 Missing_value_placeholder の右端のインスタンスは保持されます。

## <a name="syntax"></a>構文

`series_fill_backward(`*x* `[, `*missing_value_placeholder*`])`
* は、すべてのインスタンスが後方に入力されている*missing_value_placeholder*の系列*x*を返します。

## <a name="arguments"></a>引数

* *x*: 数値の配列である動的配列スカラー式。
* *missing_value_placeholder*: この省略可能なパラメーターは、欠損値のプレースホルダーを指定します。 既定値は `double` (*null*) です。

**メモ**

* [Make シリーズ](make-seriesoperator.md)の後に補間関数を適用するには、 *null*を既定値として指定します。 

```kusto
make-series num=count() default=long(null) on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```

* *Missing_value_placeholder*は、実際の要素型に変換される任意の型にすることができます。 `double`(*Null*)、 `long` (*null*)、 `int` (*null*) の両方が同じ意味を持ちます。
* *Missing_value_placeholder*が `double` (*null*) の場合 (または省略されたが同じ意味を持つ場合)、結果に*null*値が含まれる可能性があります。 これらの*null*値を埋めるには、他の補間関数を使用します。 現時点では、 [series_outliers ()](series-outliersfunction.md)のみが入力配列で*null*値をサポートしています。
* 関数は、元の型の配列要素を保持します。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let data = datatable(arr: dynamic)
[
    dynamic([111,null,36,41,null,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_forward = series_fill_backward(arr)

```

|`arr`|`fill_forward`|
|---|---|
|[111、null、36、41、null、null、16、61、33、null、null]|[111、36、36、41、16、16、16、61、33、null、null]|

  
上記の配列の補間を完了するには、 [series_fill_forward](series-fill-forwardfunction.md)または[series-fill-const](series-fill-constfunction.md)を使用します。
