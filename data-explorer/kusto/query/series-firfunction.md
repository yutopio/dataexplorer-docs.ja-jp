---
title: series_fir ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_fir () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: ef72ce93dd0cc6d4ab95c46365bfb0351d9d565a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87343976"
---
# <a name="series_fir"></a>series_fir()

系列に有限インパルス応答 (FIR) フィルターを適用します。  

関数は、入力として動的な数値配列を含む式を受け取り、[有限インパルス応答](https://en.wikipedia.org/wiki/Finite_impulse_response)フィルターを適用します。 係数を指定することによって、 `filter` 移動平均、スムージング、変更検出、その他多くのユースケースを計算するために使用できます。 関数は、動的配列とフィルターの係数の静的動的配列を含む列を入力として受け取り、列にフィルターを適用します。 フィルター処理された出力を含む、動的配列の新しい列が出力されます。  

## <a name="syntax"></a>構文

`series_fir(`*x* `,` *フィルター* [ `,` *正規化*[ `,` *中央*]]`)`

## <a name="arguments"></a>引数

* *x*: 数値の動的配列のセル。 通常、結果として得られるのは、[系列](make-seriesoperator.md)または[make_list](makelist-aggfunction.md)演算子の出力です。
* *フィルター*: フィルターの係数を含む定数式です (数値の動的配列として格納されます)。
* *ノーマライズ*: フィルターを正規化する必要があるかどうかを示す省略可能なブール値。 つまり、係数の合計で除算されます。 フィルターに負の値が含まれている場合は、*正規化*をとして指定する必要があり `false` ます。それ以外の場合、結果はになり `null` ます。 指定しない場合、*フィルター*に負の値があるかどうかによって、既定値の*ノーマライズ*が想定されます。 *フィルター*に負の値が少なくとも1つ含まれている場合、*ノーマライズ*はと見なされ `false` ます。  
正規化は、係数の合計が1であることを確認するための便利な方法です。 その後、フィルターによってシリーズが増幅または減衰されることはありません。 たとえば、4つのビンの移動平均は、 *filter*= [1, 1, 1, 1] と*正規化*された = true で指定できますが、これは [0.25, 0.25.0.25, 0.25] を入力するよりも簡単です。
* *center*: フィルターが現在のポイントの前後の時間枠で対称的に適用されるか、現在のポイントから後方にある時間枠で適用されるかを示す、省略可能なブール値。 既定では、center は false です。これは、データのストリーミングのシナリオに適しています。ここでは、現在と以前のポイントにのみフィルターを適用できます。 ただし、アドホック処理の場合は、をに設定して、時系列との同期を維持することができ `true` ます。 以下の例を参照してください。 このパラメーターは、フィルターの[グループ遅延](https://en.wikipedia.org/wiki/Group_delay_and_phase_delay)を制御します。

## <a name="examples"></a>例

* *Filter*= [1, 1, 1, 1, 1] を設定し、*ノーマライズ* = (既定値) することで、5つのポイントの移動平均を計算し `true` ます。 *Center* = `false` (既定) `true` との効果に注意してください。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
*5h_MovingAvg_centered*: 同じですが、を設定すると、 `center=true` ピークは元の場所に残ります。

:::image type="content" source="images/series-firfunction/series-fir.png" alt-text="シリーズ fir" border="false":::

* ポイントとその前の点との差を計算するには、set *filter*= [1,-1] を設定します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range t from bin(now(), 1h)-11h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS',t,value=dynamic([0,0,0,0,2,2,2,2,3,3,3,3])
| extend diff=series_fir(value, dynamic([1,-1]), false, false)
| render timechart
```

:::image type="content" source="images/series-firfunction/series-fir2.png" alt-text="シリーズ fir 2" border="false":::
