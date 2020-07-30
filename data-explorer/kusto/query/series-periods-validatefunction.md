---
title: series_periods_validate ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_periods_validate () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 24b47981e90c15e8a0f295d845ca28a03f324a88
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351303"
---
# <a name="series_periods_validate"></a>series_periods_validate()

時系列に特定の長さの定期的なパターンが含まれているかどうかを確認します。  

多くの場合、アプリケーションのトラフィックを測定するメトリックは、週単位または日単位で表されます。 この期間は、 `series_periods_validate()` 週単位と日単位のチェックを実行することで確認できます。

関数は、タイムシリーズの動的配列 (通常は、[系列](make-seriesoperator.md)演算子の結果の出力) と、 `real` 検証する期間の長さを定義する1つ以上の数値を含む列を入力として受け取ります。

関数は、次の2つの列を出力します。
* *期間*: 検証する期間 (入力で指定) を格納する動的配列。
* *スコア*: 0 ~ 1 のスコアを含む動的配列。 このスコア*は、期間配列内*のそれぞれの位置における期間の有意性を示します。

## <a name="syntax"></a>構文

`series_periods_validate(`*x* `,` *period1* [ `,` *period2* `,` 。 . . ] `)`

## <a name="arguments"></a>引数

* *x*: 数値の配列である動的配列スカラー式。通常は、[対](make-seriesoperator.md)数演算子または[make_list](makelist-aggfunction.md)演算子の結果の出力です。
* *period1*、 *period2*など: `real` 検証する期間をビンサイズの単位で指定する数値。 たとえば、系列が1h ビンにある場合、週単位の期間は168ビンになります。

> [!IMPORTANT]
> * 各*期間*引数の最小値は**4**で、最大値は入力系列の半分です。 これらの範囲外の*ピリオド*引数の場合、出力スコアは**0**になります。
>
> * 入力した時系列は、定期的である必要があります。つまり、定数ビンで集計され、[系列](make-seriesoperator.md)を使用して作成されている場合は常にです。 そうでない場合、出力は意味のないものになります。
> 
> * 関数は、最大16の期間を受け入れて検証します。

## <a name="example"></a>例

次のクエリは、1日に2回集計される、アプリケーションのトラフィックの月のスナップショットを埋め込みます (ビンのサイズは12時間です)。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/series-periods/series-periods.png" alt-text="シリーズ期間":::

このシリーズでを実行して `series_periods_validate()` 週単位の期間 (14 ポイントの長さ) を検証すると、5日間 (10 ポイントの長さ) を検証するときにスコアが高くなり、スコアが**0**になります。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_validate(y, 14.0, 10.0)
```

| 系列の \_ 期間で \_ \_ y 期間を検証 \_ する  | 系列の \_ 期間で \_ \_ y スコアを検証 \_ する |
|-------------|-------------------|
| [14.0, 10.0] | [0.84, 0.0]  |
