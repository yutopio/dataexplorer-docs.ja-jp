---
title: series_fir ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの series_fir () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 4641da6481365d13919de59708387b689581c9ce
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618805"
---
# <a name="series_fir"></a>series_fir()

系列に有限インパルス応答フィルターを適用します。  

動的な数値配列を含む式を入力として受け取り、[有限インパルス応答](https://en.wikipedia.org/wiki/Finite_impulse_response)フィルターを適用します。 `filter`係数を指定することによって、移動平均、スムージング、変更検出、その他多くのユースケースを計算するために使用できます。 この関数は、動的配列とフィルターの係数の静的な動的配列を含む列を入力として受け取り、列にフィルターを適用します。 フィルター処理された出力を含む、動的配列の新しい列が出力されます。  

**構文**

`series_fir(`*x* `,` *フィルター* [`,` *正規化*[`,` *中央*]]`)`

**引数**

* *x*: 数値の配列である動的配列のセル。通常は、[系列](make-seriesoperator.md)または[make_list](makelist-aggfunction.md)の演算子の結果の出力です。
* *フィルター*: フィルターの係数を含む定数式です (数値の動的配列として格納されます)。
* *ノーマライズ*: フィルターを正規化する (つまり、係数の合計で除算する) かどうかを示す省略可能なブール値。 *フィルター*に負の値が含まれている場合`false`、*正規化*はとし`null`て指定する必要があります。それ以外の場合、結果はになります。 指定しない場合、*フィルター*に負の値があるかどうかによって既定値の*ノーマライズ*が想定されます。*フィルター*に負の値が1つ以上含まれている場合、*ノーマライズ*はと見なされ`false`ます。  
正規化は、係数の合計が1であることを確認するのに便利な方法であり、フィルターによってシリーズが増幅または減衰されないようにします。 たとえば、4つのビンの移動平均を*フィルター*= [1, 1, 1, 1] に指定し、*正規化*された = true にすると、[0.25, 0.25.0.25, 0.25] より簡単に入力できます。
* *center*: フィルターが現在のポイントの前後の時間枠で対称的に適用されるか、現在のポイントから後方にある時間枠で適用されるかを示す、省略可能なブール値。 既定では center は false です。これはデータのストリーミングのシナリオに適合します。この場合、現在のポイントとそれより前のポイントにのみフィルターを適用できます。ただし、アドホック処理の場合、これを true に設定して、時系列との同期を維持できます (以下の例を参照)。 技術的に言えば、このパラメーターはフィルターの[グループ遅延](https://en.wikipedia.org/wiki/Group_delay_and_phase_delay)を制御します。

**使用例**

* 5ポイントの移動平均を計算するには、 *filter*= [1, 1, 1, 1, 1] と*ノーマライズ*= `true` (既定値) を設定します。 *center* = Center`false` (既定) との効果に注意し`true`てください。

```kusto
range t from bin(now(), 1h)-23h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS', val=dynamic([0,0,0,0,0,0,0,0,0,10,20,40,100,40,20,10,0,0,0,0,0,0,0,0]), t
| extend 5h_MovingAvg=series_fir(val, dynamic([1,1,1,1,1])),
         5h_MovingAvg_centered=series_fir(val, dynamic([1,1,1,1,1]), true, true)
| render timechart
```

このクエリは次の結果を返します。  
*5h_MovingAvg*: 5 ポイント移動平均フィルター。 スパイクを平滑化し、ピークを (5-1)/2 = 2h ずらします。  
*5h_MovingAvg_centered*: 同じですが、center = true に設定すると、ピークが元の場所にとどまります。

:::image type="content" source="images/series-firfunction/series-fir.png" alt-text="シリーズ fir" border="false":::

* Point とその前の点の差を計算するには、 *filter*= [1,-1] を設定します。

```kusto
range t from bin(now(), 1h)-11h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS',t,value=dynamic([0,0,0,0,2,2,2,2,3,3,3,3])
| extend diff=series_fir(value, dynamic([1,-1]), false, false)
| render timechart
```

:::image type="content" source="images/series-firfunction/series-fir2.png" alt-text="シリーズ fir 2" border="false":::
