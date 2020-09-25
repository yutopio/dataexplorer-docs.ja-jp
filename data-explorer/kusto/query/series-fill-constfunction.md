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
ms.openlocfilehash: 7e587ab08c009516788e3a44b0b8e4a321741d94
ms.sourcegitcommit: 8853e50a798ee7c78b69bf9822bbf1ced3abe73c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91227026"
---
# <a name="series_fill_const"></a>series_fill_const()

系列の欠損値を指定された定数値に置き換えます。

動的な数値配列を含む式を入力として受け取り、missing_value_placeholder のすべてのインスタンスを指定された constant_value に置き換え、結果の配列を返します。

## <a name="syntax"></a>構文

`series_fill_const(`*x* `, `*constant_value* `[,`*missing_value_placeholder*`])`
* では、すべてのインスタンスが*constant_value*に*missing_value_placeholder*置換された系列*x*が返されます。

## <a name="arguments"></a>引数

* *x*: 数値の配列である動的配列スカラー式。
* *constant_value*: 欠損値を置換する値。 
* *missing_value_placeholder*: 省略可能な、置換対象の欠損値のプレースホルダーを指定するパラメーターです。 既定値は `double` (*null*) です。

**ノート**
* [シリーズを](make-seriesoperator.md)使用して系列を作成する場合は、既定値の0を使用して欠損値を入力します。 また、 `default = ` make series ステートメントで *DefaultValue* を指定することによって、定数値を指定することもできます。

```kusto
make-series num=count() default=-1 on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```
  
* [作成系列](make-seriesoperator.md)の後に補間関数を適用するには、既定値として*null*を指定します。 

```kusto
make-series num=count() default=long(null) on TimeStamp from ago(1d) to ago(1h) step 1h by Os, Browser
```
  
* *Missing_value_placeholder*は任意の型にすることができ、これは実際の要素の型に変換されます。 そのため、 `double` (*null*)、 `long` (*null*)、 `int` (*null*) は同じ意味を持ちます。
* 関数は、配列要素の元の型を保持します。 

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
