---
title: series_periods_validate ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの series_periods_validate () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 89ac06f2d2bbb376f08cf3fd88d316a7fcab0594
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618609"
---
# <a name="series_periods_validate"></a>series_periods_validate()

時系列に特定の長さの定期的なパターンが含まれているかどうかを確認します。  

多くの場合、アプリケーションのトラフィックを測定するメトリックは、週単位または日単位で表されます。 これは、週単位および`series_periods_validate()`日単位のチェックを実行することで確認できます。

関数は、タイムシリーズの動的配列 (通常は、[系列](make-seriesoperator.md)演算子の結果の出力) と、検証する期間の長さを定義する 1 `real`つ以上の数値を含む列を入力として受け取ります。 

関数は2つの列を出力します。
* *期間*: 検証する期間を含む動的配列 (入力で指定)
* *スコア*:*ピリオド*配列内のそれぞれの位置にある期間の有意性を測定する、0 ~ 1 の間のスコアを含む動的配列

**構文**

`series_periods_validate(`*x* `,` *period1* [ `,` *period2* period2 `,` 。 . . ] `)`

**引数**

* *x*: 数値の配列である動的配列スカラー式。通常は、[対](make-seriesoperator.md)数演算子または[make_list](makelist-aggfunction.md)演算子の結果の出力です。
* *period1*、 *period2*、その他。 `real` : ビンサイズの単位で、検証する期間を指定する数値。 たとえば、系列が1h ビンにある場合、週単位の期間は168ビンになります。

> [!IMPORTANT]
> * 各*期間*引数の最小値は**4**で、最大値は入力系列の半分の長さです。これらの範囲外の*ピリオド*引数の場合、出力スコアは**0**になります。
>
> * 入力したタイムシリーズは、標準である必要があります。つまり、定数ビンで集計されます (つまり、[シリーズ](make-seriesoperator.md)を使用して作成された場合は、常にケースです)。 そうでない場合、出力は意味のないものになります。
> 
> * 関数は、最大16の期間を受け入れて検証します。


**例**

次のクエリは、1日に2回 (つまり、ビンのサイズが12時間)、アプリケーションのトラフィックの月のスナップショットを埋め込みます。

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="シリーズ期間":::

この`series_periods_validate()`系列でを実行して週単位の期間 (14 ポイントの長さ) を検証すると、スコアは高くなり、5日の期間 (10 ポイントの長さ) を検証するとスコアが**0**になります。

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_validate(y, 14.0, 10.0)
```

| 系列\_の\_期間\_で\_y 期間を検証する  | 系列\_の\_期間\_で\_y スコアを検証する |
|-------------|-------------------|
| [14.0, 10.0] | [0.84, 0.0]  |