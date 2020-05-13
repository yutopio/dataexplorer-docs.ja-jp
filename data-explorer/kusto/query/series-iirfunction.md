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
ms.openlocfilehash: 8b03970aacafef932f6397e64afdf871dc086bc1
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372624"
---
# <a name="series_iir"></a>series_iir()

無限インパルス応答フィルターを系列に適用します。  

動的な数値配列を含む式を入力として受け取り、[無限インパルス応答](https://en.wikipedia.org/wiki/Infinite_impulse_response)フィルターを適用します。 フィルター係数を指定することによって、たとえば、系列の累積合計を計算したり、スムージング操作を適用したり、さまざまな[高パス](https://en.wikipedia.org/wiki/High-pass_filter)、[バンドパス](https://en.wikipedia.org/wiki/Band-pass_filter)、[低パス](https://en.wikipedia.org/wiki/Low-pass_filter)のフィルターを適用したりすることができます。 関数は、動的配列を含む列と、フィルターの*a*および*b*係数の2つの静的動的配列を入力として受け取り、列にフィルターを適用します。 フィルター処理された出力を含む、動的配列の新しい列が出力されます。  
 

**構文**

`series_iir(`*x* `,` *b* `,` *a*`)`

**引数**

* *x*: 数値の配列である動的配列のセル。通常は、[系列](make-seriesoperator.md)または[make_list](makelist-aggfunction.md)の演算子の結果の出力です。
* *b*: フィルターの分子係数を含む定数式 (数値の動的配列として格納されます)。
* *a*: 定数式 ( *b*など)。 フィルターの分母係数を含みます。

> [!IMPORTANT]
> (つまり) しないの最初の要素は `a` `a[0]` 0 になります (0 による除算を避けるために、次の式を参照してください)。

**フィルターの再帰式の詳細**

* 入力配列 X と係数の配列 a、b 長さ n_a および n_b が指定されている場合、出力配列 Y を生成するフィルターの転送関数はによって定義されます (Wikipedia の「」も参照してください)。

<div align="center">
Y<sub>i</sub> = a<sub>0</sub><sup>-1</sup>(b<sub>0</sub>X<sub>i</sub> 
 + b<sub>1</sub>×<sub>i-1</sub> + ... + b<sub>n<sub>b</sub>-1</sub>* i-<sub>n<sub>b</sub>-1</sub> 
 - a<sub>1</sub>y<sub>i-1</sub>-a<sub>2</sub>y i-<sub>2</sub> - ...-<sub><sub>a</sub></sub><sub><sub>a</sub>a</sub>-1 y i-n-1)
</div>

**例**

累積合計を計算するには *、係数 a*= [1,-1] と*b*= [1] を指定して iir フィルターを実行します。  

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
