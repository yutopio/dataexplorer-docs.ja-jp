---
title: series_decompose() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの series_decompose() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 375d63dd050840cd884fca0198511e71ac46f170
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663604"
---
# <a name="series_decompose"></a>series_decompose()

系列に分解変換を適用します。  

系列(動的数値配列)を入力として含む式を取り、それを季節成分、トレンド成分、残差成分に分解します。
 
**構文**

`series_decompose(`*系列*`[,`*季節*`,`*性トレンド*`,`*Seasonality_threshold**Test_points*Test_pointsSeasonality_threshold`,``])`

**引数**

* *Series*: 数値の配列である動的配列セル(通常、[系列または](make-seriesoperator.md)[make_list](makelist-aggfunction.md)演算子の結果の出力)
* *季節性*: 季節分析を制御する整数で、いずれかを含む
    * -1: [series_periods_detect](series-periods-detectfunction.md)を使用して季節性を自動検出する (デフォルト)。
    * ピリオド: ビン数で予想される期間を指定する正の整数。たとえば、系列が 1h ビンの場合、週単位の期間は 168 ビンです。
    * 0: 季節性なし(この成分の抽出をスキップ)。    
* *トレンド*: トレンド分析を制御する文字列で、次のいずれかを含みます。
    * "avg": トレンド成分を平均(x)として定義する(デフォルト)
    * 「ラインフィット」:線形回帰を使用してトレンド成分を抽出します。
    * "none": トレンドなし、このコンポーネントの抽出をスキップします。    
* *Test_points*: 0 (デフォルト) または正の整数で、学習 (回帰) プロセスから除外する系列の終点の数を指定します。 このパラメーターは、予測目的で設定する必要があります。
* *Seasonality_threshold*: 季節性が自動検出に設定されている場合の*季節性*スコアのしきい値は、既定の`0.6`スコアしきい値は です。 詳細については、「 [series_periods_detect](series-periods-detectfunction.md)」を参照してください。

**返す**

 この関数は、次の各系列を返します。

* `baseline`: 系列の予測値 (季節成分とトレンド成分の合計は以下を参照)
* `seasonal`: 季節成分のシリーズ:
    * ピリオドが検出されない場合、または明示的に 0 に設定されている場合: 定数 0
    * 検出された場合、または正の整数に設定されている場合:同じフェーズ内の系列ポイントの中央値
* `trend`: トレンドコンポーネントのシリーズ
* `residual`: 残差成分の系列(すなわちx -ベースライン)
  

**メモ**

* コンポーネントの実行順序:
    1. 季節シリーズを抽出する
    2. x から減算し、季節外れの系列を生成する
    3. 脱季節系列からトレンド成分を抽出
    4. ベースラインの作成 = 季節 + トレンド
    5. 残差 = x - ベースラインを作成します
    
* 季節性またはトレンドを有効にする必要があり、それ以外の場合は関数が冗長で、ベースライン = 0と残差 = xを返すだけです。

**シリーズ分解の詳細**

この方法は、通常、将来のメトリック値を予測したり異常な値を検出したりするために、定期的な傾向の動作(サービス トラフィックやその他の使用状況指標など)を明らかにする時系列メトリックに適用されます。 この回帰プロセスの暗黙の仮定は、(既知の)季節およびトレンドの振る舞いとは別に、時系列は確率的でランダムに分布しているということです。したがって、季節成分とトレンド成分から将来の指標値を予測することができます(残差部分を無視して)、残差部品の外れ値検出に基づいて異常値を検出することができます。 詳細は、この偉大な本の[時系列分解の章](https://www.otexts.org/fpp/6)で見つけることができます。

**使用例**

**1. 週次季節性**

次の例では、季節的な季節性を持ち、トレンドのないシリーズを生成し、それに外れ値を追加します。 `series_decompose`季節性を自動検出し、季節成分とほぼ同じベースラインを生成します。 追加した外れ値は、残差成分にはっきりと見えます。

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

:::image type="content" source="images/samples/series-decompose1.png" alt-text="シリーズ分解1":::

**2. トレンドのある週次季節性**

この例では、前の例の系列に傾向を追加します。 まず、トレンド`avg`の`series_decompose`デフォルト値が平均のみを受け取り、トレンドを計算しないデフォルトパラメータで実行すると、生成されたベースラインにはトレンドが含まれておらず、前の例と比較して正確でないことがわかります。

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

:::image type="content" source="images/samples/series-decompose2.png" alt-text="シリーズ分解2":::

次に、同じ例を実行しますが、系列のトレンドを期待しているので、トレンドパラメータ`linefit`で指定します。 正のトレンドが検出され、ベースラインが入力系列に近いことがわかります。 残差はゼロに近く、外れ値だけが目立っています。グラフには、系列のすべてのコンポーネントを表示できます。

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

:::image type="content" source="images/samples/series-decompose3.png" alt-text="シリーズ分解3":::
