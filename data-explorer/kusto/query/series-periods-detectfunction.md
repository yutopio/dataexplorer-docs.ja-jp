---
title: series_periods_detect ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_periods_detect () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: a3f2a325b63306f7fec6b11eb3e684d3918bc7d5
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372521"
---
# <a name="series_periods_detect"></a>series_periods_detect()

時系列に存在する最も重要な期間を検索します。  

多くの場合、アプリケーションのトラフィックを測定するメトリックは、週単位と日単位の2つの大きな期間で特徴付けられます。 このような時系列を指定すると、は `series_periods_detect()` これら2つの主要な期間を検出します。  
関数は、タイムシリーズの動的配列 (通常は、[系列](make-seriesoperator.md)演算子の結果として生成される出力) を含む列を入力として受け取り `real` ます。また、最小と最大の期間のサイズ (たとえば、1日の1日のサイズが24である場合は箱の数) を定義し、 `long` 検索する関数の合計期間を定義します。 関数は2つの列を出力します。
* *期間*: (ビンサイズの単位で) 検出された期間を含む動的配列。スコアの順序で並べ替えられます。
* *スコア*: 0 ~ 1 の範囲の値を含む動的配列は、*ピリオド配列内のそれぞれ*の位置にある期間の有意性を測定します。
 
**構文**

`series_periods_detect(`*x* `,` *min_period* `,` *max_period* `,` *num_periods*`)`

**引数**

* *x*: 数値の配列である動的配列スカラー式。通常は、[対](make-seriesoperator.md)数演算子または[make_list](makelist-aggfunction.md)演算子の結果の出力です。
* *min_period*: `real` 検索する最小期間を指定する数値。
* *max_period*: `real` 検索する最長期間を指定する数値。
* *num_periods*: `long` 必要な最大期間数を指定する数値。 これは出力動的配列の長さになります。

> [!IMPORTANT]
> * アルゴリズムでは、少なくとも4つのポイントと、系列の長さの半分を含む期間を検出できます。 
>
> * *Min_period*を少し下に設定し、時系列で検索する期間を少し上に*max_period*する必要があります。 たとえば、1時間ごとに集計された信号があり、毎日の > と週単位の期間 (それぞれ 24 & 168) を探している場合、 *min_period*= 0.8 \* 24、 *max_period*= 1.2 \* 168 に設定できます。これらの期間には、20% の余白が残ります。
>
> * 入力したタイムシリーズは、標準である必要があります。つまり、定数ビンで集計されます (つまり、[シリーズ](make-seriesoperator.md)を使用して作成された場合は、常にケースです)。 そうでない場合、出力は意味のないものになります。


**例**

次のクエリは、1日に2回 (つまり、ビンのサイズが12時間)、アプリケーションのトラフィックの月のスナップショットを埋め込みます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="シリーズ期間":::

`series_periods_detect()`この系列で実行すると、週単位の期間 (14 ポイントの長さ) になります。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_detect(y, 0.0, 50.0, 2)
```

| 系列の期間による \_ \_ \_ y 期間の検出 \_  | 系列の \_ 期間が \_ \_ y 期間の \_ \_ スコアを検出する |
|-------------|-------------------|
| [14.0, 0.0] | [0.84, 0.0]  |


サンプリングの粒度が粗い (12 時間 bin size) ため、グラフにも表示される日単位の期間が見つからないことに注意してください。これは、2つのビンの1日の期間が、アルゴリズムに必要な4ポイントの最小期間サイズにベルされているためです。
