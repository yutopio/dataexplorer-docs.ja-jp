---
title: series_fill_forward() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで series_fill_forward() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 6c79733aa1abf001f52eb07606c866904e370906
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508672"
---
# <a name="series_fill_forward"></a>series_fill_forward()

系列内の欠損値の順方向フィル補間を実行します。

動的な数値配列を含む式を入力として受け取り、missing_value_placeholderのすべてのインスタンスをmissing_value_placeholder以外の左側の最も近い値に置き換え、結果の配列を返します。 missing_value_placeholderの左端のインスタンスは保持されます。

**構文**

`series_fill_forward(`*x*`[, `*missing_value_placeholder*`])`
* missing_value_placeholderの全インスタンス*が前方に*埋まったシリーズ*x*を返します。

**引数**

* *x*: 数値の配列である動的配列スカラー式。 
* *missing_value_placeholder*: 欠損値を置き換えるプレースホルダを指定するオプションパラメータです。 デフォルト値は`double`(*null*) です。

**メモ**

* [make-series](make-seriesoperator.md)の後に補間関数を適用するためには、デフォルト値として*null*を指定することをお勧めします。 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```

* *missing_value_placeholder*は、実際の要素型に変換される任意の型を指定できます。 `double`したがって、 ( `long`*null*) `int`(*null*) または (*null*) は同じ意味を持ちます。
* *missing_value_placeholder*が`double`(*null*) (または同じ意味を持つ単に省略された) 場合、結果には*NULL*値が含まれる可能性があります。 他の補間関数を使用して、それらを埋めます。 現在[、series_outliers()](series-outliersfunction.md)のみが入力配列で*null 値*をサポートしています。
* 関数は、配列要素の元の型を保持します。

**例**

```kusto
let data = datatable(arr: dynamic)
[
    dynamic([null,null,36,41,null,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_forward = series_fill_forward(arr)  

```

|Arr|fill_forward|
|---|---|
|[null,null,36,41,null,null,16,61,33,null,null]|[ヌル,ヌル,36,41,41,41,16,61,33,33,33]|
   
series_fill_backwardまたは系列[series_fill_backward](series-fill-backwardfunction.md) [fill-const](series-fill-constfunction.md)を使用して、上記の配列の補間を完了できます。