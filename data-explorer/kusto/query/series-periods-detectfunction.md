---
title: series_periods_detect ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_periods_detect () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 6e362dd7d94c57cb79ee58c8ed7b36b719030987
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252939"
---
# <a name="series_periods_detect"></a>series_periods_detect()

時系列に存在する最も重要な期間を検索します。  

多くの場合、アプリケーションのトラフィックを測定するメトリックは、週単位と日単位の2つの大きな期間によって特徴付けられます。 関数は、 `series_periods_detect()` 時系列でこれらの2つの主要な期間を検出します。  
関数は、入力としてを受け取ります。
* 時系列の動的配列を含む列。 通常、列は、 [作成系列](make-seriesoperator.md) 演算子の結果の出力です。
* `real`最小と最大の期間のサイズを定義する2つの数値 (検索するビンの数)。 たとえば、1h bin の場合、1日の期間のサイズは24になります。 
* `long`検索する関数の合計期間を定義する数値。 

関数は、次の2つの列を出力します。
* *期間*: ビンサイズの単位で、検出された期間を含む動的配列。スコアに基づいて並べ替えられます。
* *スコア*: 0 と1の間の値を格納している動的配列。 各配列 *は、period 配列内* のそれぞれの位置にある期間の有意性を測定します。
 
## <a name="syntax"></a>構文

`series_periods_detect(`*x* `,` *min_period* `,` *max_period* `,` *num_periods*`)`

## <a name="arguments"></a>引数

* *x*: 数値の配列である動的配列スカラー式。通常は、 [対](make-seriesoperator.md) 数演算子または [make_list](makelist-aggfunction.md) 演算子の結果の出力です。
* *min_period*: `real` 検索する最小期間を指定する数値。
* *max_period*: `real` 検索する最長期間を指定する数値。
* *num_periods*: `long` 必要な最大期間数を指定する数値。 この数値は、出力動的配列の長さになります。

> [!IMPORTANT]
> * アルゴリズムでは、少なくとも4つのポイントと、系列の長さの半分を含む期間を検出できます。 
>
> * *Min_period*を少し下に設定し、タイムシリーズで検索する期間を少し上に*max_period*します。 たとえば、1時間ごとに集計されたシグナルがあり、毎日と毎週の両方の期間 (それぞれ24と168時間) を検索する場合は、 *min_period*= 0.8 \* 24、 *max_period*= 1.2 168 を設定し、これらの期間を20% の余白のままにすることができ \* ます。
>
> * 入力した時系列は標準である必要があります。 つまり、常に [シリーズ](make-seriesoperator.md)を使用して作成されている場合は、定数ビンで集計されます。 そうでない場合、出力は意味のないものになります。

## <a name="example"></a>例

次のクエリは、1日に2回集計された、アプリケーションのトラフィックの月のスナップショットを埋め込みます。 ビンのサイズは12時間です。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="シリーズ期間":::

`series_periods_detect()`このシリーズで実行すると、週単位の期間は14ポイントとなります。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_detect(y, 0.0, 50.0, 2)
```

| 系列の期間による \_ \_ \_ y 期間の検出 \_  | 系列の \_ 期間が \_ \_ y 期間の \_ \_ スコアを検出する |
|-------------|-------------------|
| [14.0, 0.0] | [0.84, 0.0]  |


> [!NOTE] 
> サンプリングが粗い (12 時間 bin size) ためにグラフに表示される日単位の期間も検出されませんでした。そのため、1日のうちの2つのビンが、アルゴリズムに必要な最小の期間サイズ4ポイントを下回っています。
