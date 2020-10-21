---
title: series_decompose ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_decompose () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 2e2c2811dfa4e5b895f0c5b14a9a45b64c2a9291
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242166"
---
# <a name="series_decompose"></a>series_decompose()

系列に分解変換を適用します。  

系列 (動的な数値配列) を含む式を入力として受け取り、季節、傾向、および残余コンポーネントに分解されします。
 
## <a name="syntax"></a>構文

`series_decompose(`*シリーズ* `[,`*季節* `,` 性*傾向* `,`*Test_points* `,`*Seasonality_threshold*`])`

## <a name="arguments"></a>引数

* *Series*: 数値の配列である動的配列のセル。通常は、 [系列](make-seriesoperator.md) または [make_list](makelist-aggfunction.md) 演算子の結果の出力です。
* *季節*性: 季節分析を制御する整数 (次のいずれかを含む)
    * -1: [series_periods_detect](series-periods-detectfunction.md) (既定) を使用して季節性を自動検出します。
    * period: ビン数の予想される期間を指定する正の整数。たとえば、系列が 1-h ビンの場合、週単位の期間は168ビンです。
    * 0: 季節性はありません (このコンポーネントの抽出をスキップします)。    
* *傾向*: 次のいずれかの値を含む傾向分析を制御する文字列。
    * "avg": 傾向コンポーネントを平均 (x) として定義します (既定)。
    * "linefit": 線形回帰を使用して傾向コンポーネントを抽出します。
    * "none": 傾向がありません。このコンポーネントの抽出をスキップします。    
* *Test_points*: 0 (既定値) または正の整数。学習 (回帰) プロセスから除外する系列の末尾の点の数を指定します。 このパラメーターは、予測のために設定する必要があります。
* *Seasonality_threshold*: *季節* 性が自動検出に設定されている場合の季節性スコアのしきい値は、既定のスコアしきい値は `0.6` です。 詳細については、「 [series_periods_detect](series-periods-detectfunction.md)」を参照してください。

**Return**

 関数は、次の各系列を返します。

* `baseline`: 系列の予測値 (季節および傾向コンポーネントの合計、以下を参照)。
* `seasonal`: 季節に関連する一連のコンポーネント:
    * ピリオドが検出されない場合、または明示的に0に設定されている場合は。
    * 検出された場合、または正の整数に設定されている場合は、同じフェーズの系列ポイントの中央値。
* `trend`: 傾向コンポーネントの系列。
* `residual`: 残余コンポーネントの系列 (x ベースライン)。
  

**ノート**

* コンポーネントの実行順序:
    1. 季節シリーズを抽出する
    2. X から減算し、deseasonal シリーズを生成します。
    3. Deseasonal シリーズから傾向コンポーネントを抽出する
    4. ベースライン = 季節 + 傾向の作成
    5. 残余 = x-ベースラインを作成する
    
* 季節性と、または傾向が有効になっている必要があります。 それ以外の場合、関数は冗長であり、baseline = 0 と残余 = x を返します。

**シリーズ分解の詳細**

このメソッドは、通常、周期的な傾向または傾向の動作をマニフェストに必要とするメトリックの時系列に適用されます。 メソッドを使用して、将来のメトリック値を予測したり、異常な値を検出したりできます。 この回帰プロセスの暗黙的な前提は、季節と傾向の動作とは別に、タイムシリーズがストキャスティクスされ、ランダムに分散されることです。 残余部分を無視して、季節と傾向のコンポーネントから将来のメトリック値を予測します。 残留部分のみで外れ値の検出に基づいて異常な値を検出します。 詳細については、「 [時系列分解](https://otexts.com/fpp2/decomposition.html)」の章を参照してください。

## <a name="examples"></a>例

**週単位の季節性**

次の例では、週単位の季節性があり、傾向がないシリーズを生成します。その後、外れ値をいくつか追加します。 `series_decompose` 季節性を検出して自動的に検出し、季節コンポーネントとほぼ同じ基準を生成します。 追加した外れ値は、残余コンポーネントで明確に表示できます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 10.0, 15.0) - (((t%24)/10)*((t%24)/10)) // generate a series with weekly seasonality
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose(y)
| render timechart  
```

:::image type="content" source="images/samples/series-decompose1.png" alt-text="系列の分解1":::

**傾向がある週単位の季節性**

この例では、前の例の系列に傾向を追加します。 まず、 `series_decompose` 既定のパラメーターを使用してを実行します。 傾向の `avg` 既定値は平均のみを取得し、傾向は計算しません。 生成されたベースラインに傾向が含まれていません。 残余の傾向を観察すると、この例は前の例よりも精度が低いことが明らかになります。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose(y)
| render timechart  
```

:::image type="content" source="images/samples/series-decompose2.png" alt-text="系列の分解1":::

次に、同じ例を再実行します。 ここでは、系列に傾向があるため、 `linefit` trend パラメーターでを指定します。 正の傾向が検出され、ベースラインが入力系列に非常に近いことがわかります。 残余はゼロに近く、外れ値のみが除去されます。グラフ内の系列のすべてのコンポーネントが表示されます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose(y, -1, 'linefit')
| render timechart  
```

:::image type="content" source="images/samples/series-decompose3.png" alt-text="系列の分解1":::
