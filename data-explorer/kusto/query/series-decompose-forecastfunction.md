---
title: series_decompose_forecast ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_decompose_forecast () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 9676da9d12e2654cd4d92538f183a2630971d078
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372878"
---
# <a name="series_decompose_forecast"></a>series_decompose_forecast()

系列分解に基づく予測。

系列 (動的な数値配列) を含む式を入力として受け取り、最後の末尾の点の値を予測します (分解メソッドの詳細については、 [series_decompose](series-decomposefunction.md)を参照してください)。
 
**構文**

`series_decompose_forecast(`*シリーズ* `,`*ポイント* `[,`*季節* `,` 性*傾向* `,`*Seasonality_threshold*`])`

**引数**

* *Series*: 数値の配列である動的配列セル。 通常、結果として得られるのは、[系列](make-seriesoperator.md)または[make_list](makelist-aggfunction.md)演算子の出力です。
* *Points*: 予測する系列の末尾の点の数を指定する整数 (予測)。 これらのポイントは、learning (回帰) プロセスから除外されます。
* *季節*性: 次のいずれかを含む季節分析を制御する整数。
    * -1: [series_periods_detect](series-periods-detectfunction.md) (既定) を使用して季節性を自動検出します。 
    * period: 区間数を指定する正の整数。たとえば、系列が1h ビンにある場合、週単位の期間は168ビンになります。
    * 0: 季節性はありません (このコンポーネントの抽出をスキップします)。   
* *傾向*: 次のいずれかを含む傾向分析を制御する文字列。
    * "linefit": 線形回帰 (既定値) を使用して傾向コンポーネントを抽出します。    
    * "avg": 傾向コンポーネントを平均 (x) として定義します。
    * "none": 傾向がありません。このコンポーネントの抽出をスキップします。   
* *Seasonality_threshold*:*季節*性が自動検出に設定されている場合の季節性スコアのしきい値は、既定のスコアしきい値は `0.6` です。 詳細については、「 [series_periods_detect](series-periods-detectfunction.md)」を参照してください。

**返し**

 予測系列を含む動的配列
  

**メモ**

* 元の入力系列の動的配列には、予測する数値*ポイント*スロットが含ま[れている](make-seriesoperator.md)必要があります。通常、これは、予測する期間を含む範囲内の終了時間を指定することによって行われます。
    
* 季節性と傾向のどちらか一方または両方を有効にする必要があります。そうしないと、関数は冗長になり、ゼロで塗りつぶされた系列を返します。

**例**

次の例では、週単位の季節性と小さな上昇傾向を使用して、1時間ごとの粒度で4週間のシリーズを生成します。その後、を使用 `make-series` して、系列に空の週を追加します。 `series_decompose_forecast`は週 (24 * 7 ポイント) で呼び出され、季節性と傾向を自動的に検出し、5週間の期間全体の予測を生成します。 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let ts=range t from 1 to 24*7*4 step 1 // generate 4 weeks of hourly data
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| make-series y=max(y) on Timestamp in range(datetime(2018-03-01 05:00), datetime(2018-03-01 05:00)+24*7*5h, 1h); // create a time series of 5 weeks (last week is empty)
ts 
| extend y_forcasted = series_decompose_forecast(y, 24*7)  // forecast a week forward
| render timechart 
```

:::image type="content" source="images/series-decompose-forecastfunction/series-decompose-forecast.png" alt-text="シリーズ分解予測":::
