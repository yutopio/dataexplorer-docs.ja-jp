---
title: series_decompose_anomalies ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_decompose_anomalies () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/28/2019
ms.openlocfilehash: 2191a26a0ee0bccd708c492690e58767d3cf52e9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345625"
---
# <a name="series_decompose_anomalies"></a>series_decompose_anomalies()

異常検出は、系列の分解に基づいています。
詳細については、「 [series_decompose ()](series-decomposefunction.md)」を参照してください。

関数は、入力として系列 (動的な数値配列) を含む式を取得し、スコアを使用して異常なポイントを抽出します。

## <a name="syntax"></a>構文

`series_decompose_anomalies (`*シリーズ* `[, `*しきい値* `,`*季節* `,` 性*傾向* `, `*Test_points* `, `*AD_method* `,`*Seasonality_threshold*`])`

## <a name="arguments"></a>引数

* *Series*: 数値の配列である動的配列のセル。通常は、[系列](make-seriesoperator.md)または[make_list](makelist-aggfunction.md)演算子の結果の出力です。
* *しきい*値: 異常しきい値、軽度またはより強力な異常を検出するための既定の 1.5 (k 値)
* *季節*性: 季節分析を制御する整数 (次のいずれかを含む)
    * -1: [series_periods_detect](series-periods-detectfunction.md)を使用した、季節性の自動検出 [既定]
    * 0: 季節性はありません (つまり、このコンポーネントの抽出をスキップします)
    * 期間: ビン単位の数に予想される期間を指定する正の整数。 たとえば、系列が1時間のビンの場合、週単位の期間は168ビンになります。
* *傾向*: 次のいずれかを含む傾向分析を制御する文字列
    * "avg": 傾向コンポーネントを系列の平均として定義する [既定]
    * "none": 傾向がありません。このコンポーネントの抽出をスキップします
    * "linefit": 線形回帰を使用した傾向コンポーネントの抽出
* *Test_points*: 0 [既定] または正の整数。これは、学習 (回帰) プロセスから除外する系列の末尾の点の数を指定します。 このパラメーターは、予測のために設定する必要があります
* *AD_method*: 次のいずれかを含む残留タイムシリーズの異常検出メソッドを制御する文字列。
    * "ctukey": カスタム10パーセンタイルの範囲を持つ[Tukey のフェンステスト](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences)[default]
    * "tukey": [tukey のフェンステスト](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences)標準 25-75 パーセンタイル範囲の詳細については、「」を参照してください[series_outliers](series-outliersfunction.md)
* *Seasonality_threshold*:*季節*性が自動検出に設定されている場合の季節性スコアのしきい値。 既定のスコアのしきい値は `0.6` です。 詳細については、「」を参照してください[series_periods_detect](series-periods-detectfunction.md)

**Return**

 関数は、次の各系列を返します。

* `ad_flag`: (+ 1,-1, 0) を含む三項系列は、それぞれ、上下または非異常をマークします。
* `ad_score`: 異常スコア
* `baseline`: 分解に基づく、系列の予測された値

## <a name="the-algorithm"></a>アルゴリズム

この関数は、次の手順に従います。
1. ベースラインと残余シリーズを作成するために、各パラメーターを使用して[series_decompose ()](series-decomposefunction.md)を呼び出します。
1. [Series_outliers ()](series-outliersfunction.md)を、残余シリーズで選択した異常検出方法と共に適用して、ad_score シリーズを計算します。
1. Ad_score にしきい値を適用することによって、ad_flag の系列を計算し、それぞれをマークアップ/ダウン/異常なしにマークします。
 
## <a name="examples"></a>例

### <a name="detect-anomalies-in-weekly-seasonality"></a>週単位の季節性で異常を検出する

次の例では、週単位の季節性を持つ系列を生成し、それに外れ値を追加します。 `series_decompose_anomalies`周期性を自動検出し、繰り返しパターンをキャプチャするベースラインを生成します。 追加した外れ値は、ad_score コンポーネントで明確に把握できます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

### <a name="detect-anomalies-in-weekly-seasonality-with-trend"></a>傾向がある週単位の季節性の異常を検出する

この例では、前の例の系列に傾向を追加します。 最初に、既定のパラメーターを指定してを実行します `series_decompose_anomalies` 。既定のパラメーターでは、trend の `avg` 既定値は平均のみを使用し、傾向は計算されません。 生成されたベースラインには、前の例と比較して、傾向が含まれておらず、正確ではありません。 そのため、データに挿入した外れ値の中には、分散が高いために検出されないものがあります。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

次に、同じ例を実行しますが、系列の傾向が予想されるため、 `linefit` trend パラメーターでを指定します。 ベースラインが入力系列に非常に近いことがわかります。 挿入されたすべての外れ値が検出され、いくつかの偽陽性も検出されます。 しきい値の調整については、次の例を参照してください。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

### <a name="tweak-the-anomaly-detection-threshold"></a>異常検出のしきい値を調整する

前の例では、いくつかのノイズポイントが異常として検出されました。 次に、異常検出のしきい値を既定値の1.5 から2.5 に増やします。 このようなインターパーセンタイルの範囲を使用して、より強力な異常のみが検出されるようにします。 これで、データに挿入した外れ値のみが検出されます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
