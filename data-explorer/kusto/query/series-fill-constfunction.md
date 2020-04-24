---
title: series_fill_const() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで series_fill_const() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c7f5f939068737bdd6519fc0c63b663d19ae076a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508774"
---
# <a name="series_fill_const"></a>series_fill_const()

系列内の欠損値を指定した定数値に置き換えます。

動的な数値配列を含む式を入力として受け取り、missing_value_placeholderのすべてのインスタンスを指定されたconstant_valueに置き換え、結果の配列を返します。

**構文**

`series_fill_const(`*x*`[, `*constant_value* `[,` *missing_value_placeholder*`]])`
* constant_value で置き換えられた*missing_value_placeholder*のすべてのインスタンスを含*む系列* *x*を返します。

**引数**

* *x*: 数値の配列である動的配列スカラー式。
* *constant_value*: 欠損値を置き換えるプレースホルダを指定するパラメータです。 既定値は*0*です。 
* *missing_value_placeholder*: 欠損値を置き換えるプレースホルダを指定するオプションパラメータです。 デフォルト値は`double`(*null*) です。

**メモ**
* `default = ` *DefaultValue*構文を使用して 1 回の呼び出しで定数を埋める系列を作成できます (または 0 を想定する省略)。 詳細については[、メイクシリーズ](make-seriesoperator.md)を参照してください。

```kusto
make-series num=count() default=-1 on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```
  
* [make-series](make-seriesoperator.md)の後に補間関数を適用するためには、デフォルト値として*null*を指定することをお勧めします。 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```
  
* *missing_value_placeholder*は、実際の要素型に変換される任意の型を指定できます。 `double`したがって、 ( `long`*null*) `int`(*null*) または (*null*) は同じ意味を持ちます。
* この関数は、配列要素の元の型を保持します。 

**例**

```kusto
let data = datatable(arr: dynamic)
[
    dynamic([111,null,36,41,23,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_const1 = series_fill_const(arr, 0.0),
          fill_const2 = series_fill_const(arr, -1)  
```

|Arr|fill_const1|fill_const2|
|---|---|---|
|[111,ヌル,36,41,23,ヌル,16,61,33,ヌル,ヌル]|[111,0.0,36,41,23,0.0,16,61,33,0.0,0.0]|[111,-1,36,41,23,-1,16,61,33,-1,-1]|