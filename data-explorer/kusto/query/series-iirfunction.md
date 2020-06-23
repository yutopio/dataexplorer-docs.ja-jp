---
title: series_iir ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_iir () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: fbdf7b1a9a9f5b65e6c6ee7a78fe64afba2893af
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264795"
---
# <a name="series_iir"></a>series_iir()

無限インパルス応答フィルターを系列に適用します。  

関数は、入力としての動的な数値配列を含む式を受け取り、[無限インパルス応答](https://en.wikipedia.org/wiki/Infinite_impulse_response)フィルターを適用します。 フィルター係数を指定することで、関数を使用できます。
* 系列の累積合計を計算するには
* スムージング操作を適用するには
* さまざまな[高パス](https://en.wikipedia.org/wiki/High-pass_filter)、[バンドパス](https://en.wikipedia.org/wiki/Band-pass_filter)、および[低パス](https://en.wikipedia.org/wiki/Low-pass_filter)のフィルターを適用するには

関数は、動的配列を含む列と、フィルターの*a*および*b*係数の2つの静的動的配列を入力として受け取り、列にフィルターを適用します。 フィルター処理された出力を含む、動的配列の新しい列が出力されます。  

**構文**

`series_iir(`*x* `,` *b* `,` *a*`)`

**引数**

* *x*: 数値の配列である動的配列のセル。通常は、[系列](make-seriesoperator.md)または[make_list](makelist-aggfunction.md)演算子の結果の出力です。
* *b*: フィルターの分子係数を含む定数式 (数値の動的配列として格納されます)。
* *a*: 定数式 ( *b*など)。 フィルターの分母係数を含みます。

> [!IMPORTANT]
> `a`0 による除算を避けるため、の最初の要素 (つまり、 `a[0]` ) は0にしないます。 次の[式](#the-filters-recursive-formula)を参照してください。

## <a name="the-filters-recursive-formula"></a>フィルターの再帰式

* 入力配列 X について考えてみます。係数として、n_a と n_b の長さをそれぞれ表します。 出力配列 Y を生成するフィルターの転送関数は、次の方法で定義されます。

<div align="center">
Y<sub>i</sub> = a<sub>0</sub><sup>-1</sup>(b<sub>0</sub>X<sub>i</sub> 
 + b<sub>1</sub>×<sub>i-1</sub> + ... + b<sub>n<sub>b</sub>-1</sub>* i-<sub>n<sub>b</sub>-1</sub> 
 - a<sub>1</sub>y<sub>i-1</sub>-a<sub>2</sub>y i-<sub>2</sub> - ...-<sub><sub>a</sub></sub><sub><sub>a</sub>a</sub>-1 y i-n-1)
</div>

## <a name="example"></a>例

累積合計を計算します。 係数*a*= [1,-1] と*b*= [1] の iir フィルターを使用します。  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let x = range(1.0, 10, 1);
print x=x, y = series_iir(x, dynamic([1]), dynamic([1,-1]))
| mv-expand x, y
```

| x | y |
|:--|:--|
|1.0|1.0|
|2.0|3.0|
|3.0|6.0|
|4.0|10.0|

関数にラップする方法を次に示します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let vector_sum=(x:dynamic)
{
  let y=array_length(x) - 1;
  toreal(series_iir(x, dynamic([1]), dynamic([1, -1]))[y])
};
print d=dynamic([0, 1, 2, 3, 4])
| extend dd=vector_sum(d)
```

|d            |dd  |
|-------------|----|
|`[0,1,2,3,4]`|`10`|
