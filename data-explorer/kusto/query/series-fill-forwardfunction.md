---
title: series_fill_forward ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_fill_forward () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7ea5210f0370b495c48615d28e763bf6e396d46e
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763701"
---
# <a name="series_fill_forward"></a>series_fill_forward()

系列内の欠損値の前方塗りつぶし補間を実行します。

動的な数値配列を含む式は入力です。 関数は、missing_value_placeholder のすべてのインスタンスを missing_value_placeholder ではなく左端から最も近い値に置き換え、結果の配列を返します。 Missing_value_placeholder の左端のインスタンスは保持されます。

**構文**

`series_fill_forward(`*x* `[, `*missing_value_placeholder*`])`
* は、 *missing_value_placeholder*のすべてのインスタンスを含む系列*x*を返します。

**引数**

* *x*: 数値の配列である動的配列スカラー式。 
* *missing_value_placeholder*: 省略可能なパラメーター。置換する欠損値のプレースホルダーを指定します。 既定値は `double` (*null*) です。

**ノート**

* [Make シリーズ](make-seriesoperator.md)の後に補間関数を適用するには、 *null*を既定値として指定します。 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
make-series num=count() default=long(null) on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```

* *Missing_value_placeholder*は、実際の要素型に変換される任意の型にすることができます。 (Null) `double` と (null) の両方*null* `long` *null* `int` が同じ意味を持ちます。*null*
* Missing_value_placeholder が (null) の場合 (または省略されていて同じ意味を持つ)、結果に*null*値が含まれる場合があります。 これらの*null*値を埋めるには、他の補間関数を使用します。 現時点では、 [series_outliers ()](series-outliersfunction.md)のみが入力配列で*null*値をサポートしています。
* これらの関数は、元の型の配列要素を保持します。

**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let data = datatable(arr: dynamic)
[
    dynamic([null,null,36,41,null,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_forward = series_fill_forward(arr)  

```

|`arr`|`fill_forward`|
|---|---|
|[null、null、36、41、null、null、16、61、33、null、null]|[null、null、36、41、41、41、16、61、33、33、33]|
   
上記の配列の補間を完了するには、 [series_fill_backward](series-fill-backwardfunction.md)または[series-fill-const](series-fill-constfunction.md)を使用します。
