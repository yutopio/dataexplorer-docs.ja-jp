---
title: series_fir() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで series_fir() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: bbdf93504be431d77c19a881cf79d1e1d3d400ac
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663568"
---
# <a name="series_fir"></a>series_fir()

シリーズに有限インパルス応答フィルタを適用します。  

動的な数値配列を含む式を入力として受け取り、[有限インパルス応答](https://en.wikipedia.org/wiki/Finite_impulse_response)フィルタを適用します。 係数を`filter`指定することで、移動平均、平滑化、変化検出、その他多くのユースケースの計算に使用できます。 この関数は、動的配列とフィルターの係数の静的な動的配列を含む列を入力として受け取り、列にフィルターを適用します。 フィルター処理された出力を含む、動的配列の新しい列が出力されます。  

**構文**

`series_fir(`*x*`,`*filter*フィルタ`,`[`,` *正規化*[*中央*] ]`)`

**引数**

* *x*: 数値の配列である動的配列セルで、通常[は系列の作成](make-seriesoperator.md)または[make_list](makelist-aggfunction.md)演算子の結果として出力されます。
* *filter*: フィルタの係数を含む定数式(数値の動的配列として格納されます)。
* *正規化*: フィルタを正規化するかどうかを示すオプションのブール値 (係数の合計で割った値)。 *フィルタ*に負の値が含まれている場合、*正規化*を`false`として指定する必要`null`があります。 指定しない場合、*フィルター*に負の値が 1 つ*含まれている場合*、*正規化*は . *normalize* `false`  
正規化は係数の合計が 1 であり、フィルターが系列を増幅または減衰しないようにする便利な方法です。 たとえば、4 つのビンの移動平均は *、フィルタ*=[1,1,1,1] で指定し、*正規化*=true を指定すると、[0.25,0.25.0.25,0.25]と入力するよりも簡単です。
* *center*: フィルタが現在のポイントの前後のタイム ウィンドウに対称的に適用されるか、現在のポイントから後方にタイム ウィンドウに適用されるかを示すオプションのブール値。 既定では center は false です。これはデータのストリーミングのシナリオに適合します。この場合、現在のポイントとそれより前のポイントにのみフィルターを適用できます。ただし、アドホック処理の場合、これを true に設定して、時系列との同期を維持できます (以下の例を参照)。 技術的には、このパラメータはフィルタの[グループ遅延](https://en.wikipedia.org/wiki/Group_delay_and_phase_delay)を制御します。

**使用例**

* 5ポイントの移動平均を計算するには、*フィルタ*=[1,1,1,1]を設定し、*正規化*=`true`(デフォルト)を行います。 *center*=中心`false`(デフォルト)と`true`:

```kusto
range t from bin(now(), 1h)-23h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS', val=dynamic([0,0,0,0,0,0,0,0,0,10,20,40,100,40,20,10,0,0,0,0,0,0,0,0]), t
| extend 5h_MovingAvg=series_fir(val, dynamic([1,1,1,1,1])),
         5h_MovingAvg_centered=series_fir(val, dynamic([1,1,1,1,1]), true, true)
| render timechart
```

このクエリは次の結果を返します。  
*5h_MovingAvg:* 5ポイント移動平均フィルタ。 スパイクを平滑化し、ピークを (5-1)/2 = 2h ずらします。  
*5h_MovingAvg_centered*: 同じですが、center=true を設定すると、ピークは元の位置に留まる。

:::image type="content" source="images/samples/series-fir.png" alt-text="シリーズモミ":::

* ポイントと前の点との差を計算するには、*フィルタ*=[1,-1] を設定します。

```kusto
range t from bin(now(), 1h)-11h to bin(now(), 1h) step 1h
| summarize t=make_list(t)
| project id='TS',t,value=dynamic([0,0,0,0,2,2,2,2,3,3,3,3])
| extend diff=series_fir(value, dynamic([1,-1]), false, false)
| render timechart
```
:::image type="content" source="images/samples/series-fir2.png" alt-text="シリーズモミ2":::
