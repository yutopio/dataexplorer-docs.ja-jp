---
title: series_outliers() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでseries_outliers() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 47e479ce7fe09b2456405ac3f7daed4721374f32
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663455"
---
# <a name="series_outliers"></a>series_outliers()

系列の異常ポイントをスコア付けします。

動的な数値配列を含む式を入力として受け取り、同じ長さの動的な数値配列を生成します。 配列の各値は[、Tukey](https://en.wikipedia.org/wiki/Outlier#Tukey.27s_test)のテストを使用して起こりうる異常のスコアを示します。 入力の同じ要素で 1.5 より大きいまたはより -1.5 より小さい値では、それぞれ増加異常または減少異常を示します。   

**構文**

`series_outliers(`*x*`, `*種類*`, `*ignore_valmin_percentilemax_percentile**ignore_val*`, ``, `*max_percentile*`)`

**引数**

* *x*: 数値の配列である動的配列セル
* *種類*: 外れ値検出のアルゴリズム。 現在サポート`"tukey"`(伝統的な Tukey) と`"ctukey"`(カスタム Tukey)。 既定値は `"ctukey"` です
* *ignore_val*: 系列内の欠損値を示す数値は、デフォルトは double(null) です。 NULL 値と無視値のスコアは に`0`設定されます。
* *min_percentile*: 通常の分位間の範囲の計算の場合、デフォルトは 10、サポートされるカスタム値`[2.0, 98.0]`は`ctukey`範囲内 (のみ) 
* *max_percentile*: 同じ、デフォルトは 90、サポートされている`[2.0, 98.0]`カスタム値は範囲内にある (ctukey のみ) 

と の違いについては、`"tukey"`次`"ctukey"`の表で説明します。

| アルゴリズム | 既定の分位点範囲 | カスタム分位点範囲のサポート |
|-----------|----------------------- |--------------------------------|
| `"tukey"` | 25% / 75%              | いいえ                             |
| `"ctukey"`| 10% / 90%              | はい                            |


> [!TIP]
> この関数を使用する最も便利な方法は、[系列の作成](make-seriesoperator.md)演算子の結果に適用することです。

**例**

外れ値を作成するノイズのある時系列があり、それらの外れ値(ノイズ)を平均値に置き換えたい場合、series_outliers()を使用して外れ値を検出し、それらを置き換えることができます。

```kusto
range x from 1 to 100 step 1 
| extend y=iff(x==20 or x==80, 10*rand()+10+(50-x)/2, 10*rand()+10) // generate a sample series with outliers at x=20 and x=80
| summarize x=make_list(x),series=make_list(y)
| extend series_stats(series), outliers=series_outliers(series)
| mv-expand x to typeof(long), series to typeof(double), outliers to typeof(double)
| project x, series , outliers_removed=iff(outliers > 1.5 or outliers < -1.5, series_stats_series_avg , series ) // replace outliers with the average
| render linechart
``` 

:::image type="content" border="false" source="images/samples/series-outliers.png" alt-text="シリーズ外れ値":::
