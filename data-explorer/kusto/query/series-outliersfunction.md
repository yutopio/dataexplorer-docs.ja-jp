---
title: series_outliers ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_outliers () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 80e20e70bc51045f68fd3ef2068f099d750b2b3f
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512437"
---
# <a name="series_outliers"></a>series_outliers()

系列内の異常点をスコア付けします。

関数は、入力として動的な数値配列を含む式を受け取り、同じ長さの動的な数値配列を生成します。 配列の各値は、 ["Tukey のテスト"](https://en.wikipedia.org/wiki/Outlier#Tukey.27s_test)を使用して発生する可能性のある異常のスコアを示します。 入力の同じ要素の値が1.5 より大きい場合、異常が増加または拒否されたことを示します。 -1.5 未満の値は、異常が減少したことを示します。

**構文**

`series_outliers(`*x* `, `*種類* `, `*ignore_val* `, `*min_percentile* `, `*max_percentile*`)`

**引数**

* *x*: 数値の配列である動的配列セル
* *kind*: 外れ値検出のアルゴリズム。 は現在 `"tukey"` (従来の "Tukey") と `"ctukey"` (カスタム "tukey") をサポートしています。 既定値は `"ctukey"` です
* *ignore_val*: 系列内の欠損値を示す数値。 既定値は double (null) です。 Null 値と ignore 値のスコアはに設定されます。`0`
* *min_percentile*: 通常の分位点の範囲を計算するために使用します。 既定値は10で、サポートされているカスタム値は範囲内 `[2.0, 98.0]` ( `ctukey` のみ) です。
* *max_percentile*: 同じ、既定値は90、サポートされているカスタム値は範囲内 `[2.0, 98.0]` (ctukey のみ)

次の表では、との違いについて説明し `"tukey"` `"ctukey"` ます。

| アルゴリズム | 既定の分位点範囲 | カスタム分位点範囲のサポート |
|-----------|----------------------- |--------------------------------|
| `"tukey"` | 25% / 75%              | いいえ                             |
| `"ctukey"`| 10% / 90%              | はい                            |

> [!TIP]
> この関数を使用する最良の方法は、[系列の作成](make-seriesoperator.md)演算子の結果に適用することです。

**例**

ノイズがあるタイムシリーズでは、外れ値が発生します。 これらの外れ値 (ノイズ) を平均値で置き換えたい場合は、series_outliers () を使用して外れ値を検出し、それらを置き換えます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 100 step 1 
| extend y=iff(x==20 or x==80, 10*rand()+10+(50-x)/2, 10*rand()+10) // generate a sample series with outliers at x=20 and x=80
| summarize x=make_list(x),series=make_list(y)
| extend series_stats(series), outliers=series_outliers(series)
| mv-expand x to typeof(long), series to typeof(double), outliers to typeof(double)
| project x, series , outliers_removed=iff(outliers > 1.5 or outliers < -1.5, series_stats_series_avg , series ) // replace outliers with the average
| render linechart
``` 

:::image type="content" source="images/series-outliersfunction/series-outliers.png" alt-text="系列の外れ値" border="false":::
