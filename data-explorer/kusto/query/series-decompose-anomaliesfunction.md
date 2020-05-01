---
title: series_decompose_anomalies ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの series_decompose_anomalies () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/28/2019
ms.openlocfilehash: 51ac499690323b1d2bafb4dc20ab7773f5c99c63
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618908"
---
# <a name="series_decompose_anomalies"></a>series_decompose_anomalies()

系列分解に基づく異常検出 ( [series_decompose ()](series-decomposefunction.md)を参照) 

系列 (動的な数値配列) を含む式を入力として受け取り、スコアを持つ異常なポイントを抽出します。

**構文**

`series_decompose_anomalies (`*シリーズ* `[, ` *しきい値*`,` *の季節*`,`性の*傾向*`, ` *Test_points* `, ` *AD_method* *Seasonality_threshold* Seasonality_threshold`,``])`

**引数**

* *Series*: 数値の配列である動的配列のセル。通常は、[系列](make-seriesoperator.md)または[make_list](makelist-aggfunction.md)演算子の結果の出力です。
* *しきい*値: 異常しきい値、軽度またはより強力な異常を検出するための既定の 1.5 (k 値)
* *季節*性: 季節分析を制御する整数 (次のいずれかを含む)
    * -1: [series_periods_detect](series-periods-detectfunction.md)を使用した、季節性の自動検出 [既定] 
    * 0: 季節性がありません (つまり、このコンポーネントの抽出をスキップします)
    * 期間: ビン単位の数に予想される期間を指定する正の整数。 たとえば、系列が1h ビンにある場合、週単位の期間は168ビンになります。
* *傾向*: 次のいずれかを含む傾向分析を制御する文字列    
    * "avg": 傾向コンポーネントを系列の平均として定義する [既定]
    * "none": 傾向がありません。このコンポーネントの抽出をスキップします 
    * "linefit": 線形回帰を使用した傾向コンポーネントの抽出
* *Test_points*: 0 [既定] または正の整数。学習 (回帰) プロセスから除外する系列の末尾の点の数を指定します。 このパラメーターは、予測の目的で設定する必要があります
* *AD_method*: 次のいずれかを含む残留タイムシリーズの異常検出メソッド ( [series_outliers](series-outliersfunction.md)を参照) を制御する文字列です。    
    * "ctukey": カスタム10パーセンタイルの範囲を持つ[Tukey のフェンステスト](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences)[default]
    * "tukey": 標準 25 ~ 75 パーセンタイルの範囲での[tukey のフェンステスト](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences)
* *Seasonality_threshold*:*季節*性が自動検出に設定されている場合の季節性スコアの`0.6`しきい値は、既定のスコアしきい値です (詳細については、「」を参照してください) [series_periods_detect](series-periods-detectfunction.md)。


**返し**

 関数は、次の各系列を返します。

* `ad_flag`: (+ 1,-1, 0) を含む三項系列は、それぞれ、上下または非異常をマークします。
* `ad_score`: 異常スコア
* `baseline`: 分解に従って計算された系列の予測値

**アルゴリズムの詳細**

この関数は、次の手順に従います。
1. 各パラメーターを使用して[series_decompose ()](series-decomposefunction.md)を呼び出し、ベースラインと残余シリーズを作成します。
2. [Series_outliers ()](series-outliersfunction.md)を、残余シリーズで選択した異常検出方法と共に適用して、ad_score シリーズを計算します。
3. Ad_score にしきい値を適用することによって、ad_flag シリーズを計算します。
 
**使用例**

**1. 週単位の季節性の異常を検出する**

次の例では、週単位の季節性を持つシリーズを生成し、それに外れ値を追加します。 `series_decompose_anomalies`季節性を自動検出し、繰り返しパターンをキャプチャするベースラインを生成します。 追加した外れ値は、ad_score コンポーネントで明確に把握できます。

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

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-outliers.png" alt-text="ベースラインと外れ値を示す週単位の季節性" border="false":::

**2. 傾向がある週単位の季節性の異常を検出する**

この例では、前の例の系列に傾向を追加します。 最初に、既定`series_decompose_anomalies`のパラメーターを使用してを実行`avg`します。このパラメーターでは、傾向の既定値は平均のみを計算し、傾向は計算しません。生成されたベースラインに傾向が含まれておらず、前の例と比較しても精度が低いことがわかります。

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

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-outliers-with-trend.png" alt-text="傾向がある週単位の季節性の外れ値" border="false":::

次に、同じ例を実行しますが、系列に傾向があるため、trend パラメーター `linefit`でを指定します。 ベースラインが入力系列にかなり近いことがわかります。 挿入したすべての外れ値と、いくつかの偽陽性 (しきい値の調整に関する次の例を参照) が検出されます。

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

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-linefit-trend.png" alt-text="Linefit 傾向での週単位の季節性の異常" border="false":::

**3. 異常検出のしきい値を調整する**

前の例では、異常なポイントがいくつか検出されました。この例では、異常検出のしきい値を既定値の1.5 から2.5 の範囲内に増やし、より強力な異常のみが検出されるようにしています。 データに挿入した外れ値のみが検出されたことがわかります。

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

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-higher-threshold.png" alt-text="異常しきい値が高い週単位の異常" border="false":::

