---
title: series_iir() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで series_iir() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 96452265e07a8a8b057dc70bec520be6ab298dd3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508298"
---
# <a name="series_iir"></a>series_iir()

系列に無限インパルス応答フィルタを適用します。  

入力として動的な数値配列を含む式を取り、[無限インパルス応答](https://en.wikipedia.org/wiki/Infinite_impulse_response)フィルタを適用します。 フィルタ係数を指定することで、例えば、系列の累積和を計算し、平滑化操作、ならびに各種[ハイパス](https://en.wikipedia.org/wiki/High-pass_filter)、[バンドパス](https://en.wikipedia.org/wiki/Band-pass_filter)、[ローパス](https://en.wikipedia.org/wiki/Low-pass_filter)フィルタを適用するために使用できます。 この関数は、動的配列と、フィルターの*a*および*b*係数の 2 つの静的動的配列を含む列を入力として受け取り、列にフィルターを適用します。 フィルター処理された出力を含む、動的配列の新しい列が出力されます。  
 

**構文**

`series_iir(`*x* `,` *b* `,` *a*`)`

**引数**

* *x*: 数値の配列である動的配列セルで、通常[は系列の作成](make-seriesoperator.md)または[make_list](makelist-aggfunction.md)演算子の結果として出力されます。
* *b*: フィルタの分子係数を含む定数式(数値の動的配列として格納)。
* *a*: *b*のような定数式。 フィルターの分母係数を含みます。

> [!IMPORTANT]
> (つまり`a[0]`)`a`の最初の要素は 0 であってはなりません (0 による除算を避けるため、以下の式を参照)。

**フィルタの再帰式の詳細**

* 入力配列 X と係数配列 a、長さ b n_a、n_bをそれぞれ与えると、出力配列 Y を生成するフィルターの転送関数が定義されます (Wikipedia でも参照)。

<div align="center">
Y<sub>i</sub> =<sub>0</sub><sup>-1</sup>(b<sub>0</sub>X<sub>i</sub> 
 + b<sub>1</sub>X<sub>i-1</sub> + . + b<sub>n<sub>b</sub>-1</sub>X<sub>i-n<sub>b</sub>-1</sub> 
 - a<sub>1</sub>Y<sub>i-1</sub>-1 Y<sub><sub>a</sub></sub><sub><sub>a</sub></sub><sub>i-2.</sub> - <sub>2</sub>
</div>

**例**

累積合計の計算は、iirフィルタで係数*a*=[1,-1] と*b*=[1] で実行できます。  

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

関数にラップする方法は次のとおりです。

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