---
title: series_fill_const ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_fill_const () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 22e40c810242ee82701cf2d0e382a9f1910ed22d
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741723"
---
# <a name="series_fill_const"></a>series_fill_const()

系列の欠損値を指定された定数値に置き換えます。

動的な数値配列を含む式を入力として受け取り、missing_value_placeholder のすべてのインスタンスを指定された constant_value に置き換え、結果の配列を返します。

**構文**

`series_fill_const(`*x*`[, `*constant_value* constant_value`[,` *missing_value_placeholder*`]])`
* では、すべてのインスタンスが*constant_value*に*missing_value_placeholder*置換された系列*x*が返されます。

**引数**

* *x*: 数値の配列である動的配列スカラー式。
* *constant_value*: 置換する欠損値のプレースホルダーを指定するパラメーター。 既定値は*0*です。 
* *missing_value_placeholder*: 省略可能な、置換対象の欠損値のプレースホルダーを指定するパラメーターです。 既定値は`double`(*null*) です。

**メモ**
* `default = ` *DefaultValue*構文を使用して定数値を格納する系列を作成できます (または、を省略すると0が想定されます)。 詳細については、「[作成シリーズ](make-seriesoperator.md)」を参照してください。

```kusto
make-series num=count() default=-1 on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```
  
* [作成系列](make-seriesoperator.md)の後に補間関数を適用するには、既定値として*null*を指定します。 

```kusto
make-series num=count() default=long(null) on TimeStamp in range(ago(1d), ago(1h), 1h) by Os, Browser
```
  
* *Missing_value_placeholder*は任意の型にすることができ、これは実際の要素の型に変換されます。 そのため、 `double`(*null*)、( `long`*null*)、( `int`*null*) は同じ意味を持ちます。
* 関数は、配列要素の元の型を保持します。 

**例**

```kusto
let data = datatable(`arr`: dynamic)
[
    dynamic([111,null,36,41,23,null,16,61,33,null,null])   
];
data 
| project arr, 
          fill_const1 = series_fill_const(arr, 0.0),
          fill_const2 = series_fill_const(arr, -1)  
```

|`arr`|`fill_const1`|`fill_const2`|
|---|---|---|
|[111、null、36、41、23、null、16、61、33、null、null]|[111、0.0、36、41、23、0.0、16、61、33、0.0、0.0]|[111、-1、36、41、23、-1、16、61、33、-1、-1]|