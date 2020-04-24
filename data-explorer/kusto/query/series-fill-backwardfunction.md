---
title: series_fill_backward() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで series_fill_backward() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b8c3bb09c7067112fd358c94fd46ca85bea31358
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508740"
---
# <a name="series_fill_backward"></a>series_fill_backward()

系列内の欠損値の逆方向の埋め込み補間を実行します。

動的な数値配列を含む式を入力として受け取り、missing_value_placeholderのすべてのインスタンスをmissing_value_placeholder以外の右側の最も近い値に置き換え、結果の配列を返します。 missing_value_placeholderの右端のインスタンスは保持されます。

**構文**

`series_fill_backward(`*x*`[, `*missing_value_placeholder*`])`
* missing_value_placeholderのインスタンスがすべて逆方向*に塗りつぶ*された系列*x*を返します。

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
    dynamic([111,null,36,41,null,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_forward = series_fill_backward(arr)

```

|Arr|fill_forward|
|---|---|
|[111,ヌル,36,41,ヌル,ヌル,16,61,33,ヌル,ヌル]|[111,36,36,41,16, 16,16,61,33,ヌル,ヌル]|

  
上記配列の補間を完了するために[、series_fill_forward](series-fill-forwardfunction.md)または[系列 fill-const](series-fill-constfunction.md)を使用できます。