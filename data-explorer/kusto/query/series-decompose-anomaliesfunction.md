---
title: series_decompose_anomalies() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの series_decompose_anomalies() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/28/2019
ms.openlocfilehash: 7d9ca0585b40a97d21690f908a50de5be493c412
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663625"
---
# <a name="series_decompose_anomalies"></a>series_decompose_anomalies()

シリーズ分解に基づく異常検出[(series_decompose()](series-decomposefunction.md)を参照) 

系列 (動的数値配列) を入力として含む式を受け取り、スコアを持つ異常な点を抽出します。

**構文**

`series_decompose_anomalies (`*系列*`[, `*閾値*`,`*季節性*`,`*トレンド*`,`*Seasonality_threshold**Test_points*`, `*AD_method*Test_pointsAD_methodSeasonality_threshold`, ``])`

**引数**

* *Series*: 数値の配列である動的配列セル(通常、[系列または](make-seriesoperator.md)[make_list](makelist-aggfunction.md)演算子の結果の出力)
* *しきい値*: 異常しきい値、軽度または強い異常を検出するためのデフォルトの 1.5 (k 値)
* *季節性*: 季節分析を制御する整数で、いずれかを含む
    * -1: 季節性を自動検出[(series_periods_detect](series-periods-detectfunction.md)使用) [デフォルト] 
    * 0: 季節性なし(つまり、このコンポーネントの抽出をスキップ)
    * ピリオド: 正の整数で、予想される期間をビン単位の数で指定します。 たとえば、シリーズが 1h ビンの場合、週単位の期間は 168 ビン
* *トレンド*: トレンド分析を制御する文字列で、いずれかを含む    
    * "avg": トレンド成分を系列の平均として定義する [デフォルト]
    * "none": トレンドなし、このコンポーネントの抽出をスキップ 
    * 「ラインフィット」:線形回帰を使用してトレンド成分を抽出する
* *Test_points*: 0 [既定値] または正の整数で、学習 (回帰) プロセスから除外する系列の終点のポイント数を指定します。 このパラメータは予測目的に設定する必要があります
* *AD_method*: 残余時系列の異常検出方法[(series_outliers](series-outliersfunction.md)を参照) を制御する文字列    
    * "ctukey": カスタム 10-90 パーセンタイル範囲を持つ[チューキーのフェンス テスト](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences)[既定値]
    * 「tukey」:標準の25番目から75パーセンタイル範囲の[タキーのフェンステスト](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences)
* *Seasonality_threshold*: 季節性が自動検出に設定されている場合の*季節性*スコアのしきい値は、デフォルト`0.6`のスコアしきい値は (詳細については、 [series_periods_detect](series-periods-detectfunction.md)) です。


**返す**

 この関数は、次の各系列を返します。

* `ad_flag`: (+1,-1,0)マーキングアップ/ダウン/異常なしを含む三項シリーズ
* `ad_score`: 異常スコア
* `baseline`: 分解に基づいて系列の予測値

**アルゴリズムの詳細**

この関数は、次の手順に従います。
1. [series_decompose()](series-decomposefunction.md)をそれぞれのパラメータで呼び出し、ベースラインと残差シリーズを作成します。
2. 選択した異常検出方法ad_score残差系列に対して[series_outliers()](series-outliersfunction.md)を適用して、系列を計算します。
3. ad_scoreに対して、それぞれアップ/ダウン/ノーアノマをマークするしきい値を適用して、ad_flag系列を計算します。
 
**使用例**

**1. 週次季節性の異常の検出**

次の例では、週次季節性を持つ系列を生成し、それに外れ値を追加します。 `series_decompose_anomalies`季節性を自動検出し、反復パターンをキャプチャするベースラインを生成します。 追加した外れ値は、ad_scoreコンポーネントで明確に見つけることができます。

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 10.0, 15.0) - (((t%24)/10)*((t%24)/10)) // generate a series with weekly seasonality
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y)
| render timechart  
```

:::image type="content" source="images/samples/series-decompose-anomalies1.png" alt-text="シリーズ分解異常 1":::

**2. トレンドを伴う週次季節性の異常の検出**

この例では、前の例の系列に傾向を追加します。 まず、トレンド`avg`の`series_decompose_anomalies`デフォルト値が平均のみを受け取り、トレンドを計算しないデフォルトパラメータを使用して実行すると、生成されたベースラインにはトレンドが含まれておらず、前の例と比較して正確でないことがわかります。

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y)
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart   
```
:::image type="content" source="images/samples/series-decompose-anomalies2.png" alt-text="シリーズ分解異常 2":::

次に、同じ例を実行しますが、系列のトレンドを期待しているので、トレンドパラメータ`linefit`で指定します。 ベースラインが入力系列にはるかに近いことがわかります。 挿入したすべての外れ値が検出され、いくつかの誤検出が検出されます (次の例でしきい値を調整するを参照)。

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y, 1.5, -1, 'linefit')
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart  
```

:::image type="content" source="images/samples/series-decompose-anomalies3.png" alt-text="シリーズ分解異常 3":::

**3. 異常検出しきい値の調整**

前の例では、いくつかの騒がしい点が異常として検出され、この例では、異常検出のしきい値をデフォルトの1.5から2.5に増加させ、より強い異常のみが検出されるようにします。 データに挿入した外れ値のみが検出されたことがわかります。

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and onlgoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y, 2.5, -1, 'linefit')
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart  
```

:::image type="content" source="images/samples/series-decompose-anomalies4.png" alt-text="シリーズ分解異常 4":::
