---
title: series_periods_validate() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで series_periods_validate() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 8eba96e21513e776c984a356f88a705ca46485af
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663410"
---
# <a name="series_periods_validate"></a>series_periods_validate()

時系列に指定された長さの周期パターンが含まれているかどうかをチェックします。  

多くの場合、アプリケーションのトラフィックを測定するメトリックは、週単位および/または毎日の期間によって特徴付けられる。 これは、週単位および毎日の`series_periods_validate()`期間のチェックを実行することによって確認することができます。

この関数は、時系列の動的配列 (通常[は make-series](make-seriesoperator.md)演算子の結果の出力) を含む列と`real`、検証する期間の長さを定義する 1 つ以上の数値を入力として受け取ります。 

この関数は、2 つの列を出力します。
* *ピリオド*: 検証する期間を含む動的配列 (入力で指定)
* *scores*: 0 ~ 1 の間のスコアを含む動的配列で、期間配列内のそれぞれの位置における期間の有意性を測定*します。*

**構文**

`series_periods_validate(`*x*`,`期間`,`*1* [ 期間*2* `,` . . . ] `)`

**引数**

* *x*: 数値の配列である動的配列スカラー式(通常は[、系列の作成](make-seriesoperator.md)または[make_list](makelist-aggfunction.md)演算子の結果の出力です。
* *期間 1*、*期間 2、* など:`real`検証する期間を指定する数値 (ビン サイズの単位)。 たとえば、系列が 1h ビンの場合、週単位の期間は 168 ビンです。

> [!IMPORTANT]
> * *各期間*引数の最小値は**4**で、最大値は入力系列の長さの半分です。これらの境界の外にある*ピリオド*引数の場合、出力スコアは**0**になります。
>
> * 入力時系列は、標準の、つまり定数ビンに集約されている必要があります (これは[、make-series](make-seriesoperator.md)を使用して作成された場合に常に当てはまる)。 そうでない場合、出力は意味のないものになります。
> 
> * この関数は、検証する期間を最大 16 個まで受け入れます。


**例**

次のクエリは、1 日 2 回集計されたアプリケーションのトラフィックの月のスナップショットを埋め込みます (つまり、ビン サイズは 12 時間)。

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/samples/series-periods.png" alt-text="系列期間":::

この`series_periods_validate()`シリーズを実行して、週単位の期間 (14 ポイントの長さ) を検証すると、高いスコアが発生し、5 日間 (10 ポイントの長さ) を検証する場合は**0**スコアが得られます。

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_validate(y, 14.0, 10.0)
```

| 系列\_期間は\_\_、y\_期間を検証します。  | 系列\_期間は\_\_y\_スコアを検証します。 |
|-------------|-------------------|
| [14.0, 10.0] | [0.84,0.0]  |