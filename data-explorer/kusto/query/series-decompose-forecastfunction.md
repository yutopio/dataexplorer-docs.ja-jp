---
title: series_decompose_forecast() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの series_decompose_forecast() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 67f464949bbc432e73932c4d8bff8290ef8f0f8f
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663537"
---
# <a name="series_decompose_forecast"></a>series_decompose_forecast()

シリーズ分解に基づく予測。

系列 (動的数値配列) を含む式を入力として受け取り、最後の末尾のポイントの値を予測します (分解方法の詳細については[、series_decompose](series-decomposefunction.md)を参照)。
 
**構文**

`series_decompose_forecast(`*系列*`,`*ポイント*`,`*Seasonality_threshold**Seasonality*`,`季節性*トレンド*Seasonality_threshold `[,``])`

**引数**

* *系列*: 数値の配列である動的配列セル。 通常、[結果として生成される出力は、系列または](make-seriesoperator.md)[make_list](makelist-aggfunction.md)演算子です。
* *ポイント*: 予測する系列の終点のポイント数を指定する整数(予測)。 これらの点は学習(回帰)プロセスから除外されます。
* *季節性*: 次のいずれかを含む季節分析を制御する整数。
    * -1: [series_periods_detect](series-periods-detectfunction.md)を使用して季節性を自動検出する (デフォルト)。 
    * ピリオド: 正の整数で、予想される期間をビン数で指定します。たとえば、系列が 1h ビンの場合、週単位の期間は 168 ビンです。
    * 0: 季節性なし(この成分の抽出をスキップ)。   
* *トレンド*: トレンド分析を制御する文字列で、次のいずれかを含みます。
    * 「ラインフィット」:線形回帰(デフォルト)を使用してトレンド成分を抽出します。    
    * "avg": トレンド成分を average(x) として定義します。
    * "none": トレンドなし、このコンポーネントの抽出をスキップします。   
* *Seasonality_threshold*: 季節性が自動検出に設定されている場合の*季節性*スコアのしきい値は、既定の`0.6`スコアしきい値は です。 詳細については、「 [series_periods_detect](series-periods-detectfunction.md)」を参照してください。

**返す**

 予測シリーズを含む動的配列
  

**メモ**

* 元の入力系列の動的配列には、予測される数*ポイント*スロットが含まれている必要があり、これは通常[、make-series を](make-seriesoperator.md)使用して、予測する期間を含む範囲の終了時間を指定することによって行われます。
    
* 季節性やトレンドを有効にする必要があり、それ以外の場合は関数が冗長で、ゼロで満たされた系列を返すだけです。

**例**

次の例では、週次季節性と小さな上昇傾向を持つ毎時の穀物で一連の 4`make-series`週間を生成し、次に、系列に別の空の週を使用して追加します。 `series_decompose_forecast`は週(24*7ポイント)で呼び出され、季節性とトレンドを自動的に検出し、5週間の予報を生成します。 

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

:::image type="content" source="images/samples/series-decompose-forecast.png" alt-text="シリーズ分解予測":::
