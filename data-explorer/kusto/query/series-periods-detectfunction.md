---
title: series_periods_detect() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで series_periods_detect() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2019
ms.openlocfilehash: 0bc78f93270809774504c1eccbc9fa2bbce6c964
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663445"
---
# <a name="series_periods_detect"></a>series_periods_detect()

時系列に存在する最も重要な期間を検索します。  

多くの場合、アプリケーションのトラフィックを測定するメトリックは、週次と日次の 2 つの重要な期間によって特徴付けられています。 このような時系列を与えられ、`series_periods_detect()`これら2つの支配的な期間を検出しなければならない。  
この関数は、時系列の動的配列 (通常[は make-series](make-seriesoperator.md)演算子の結果の出力) を含`real`む列を入力し、最小および最大の期間サイズを定義する 2 つの数値 (たとえば、1 時間ビンの場合は 1 日の期間のサイズは 24)`long`を検索し、関数の合計期間数を定義する数値を入力します。 この関数は、2 つの列を出力します。
* *ピリオド*: スコア順に並べられた、見つかった期間 (ビンサイズの単位) を含む動的配列
* *scores*: 0 ~ 1 の値を含む動的配列で、各配列の各位置におけるピリオドの有意性*を測定します。*
 
**構文**

`series_periods_detect(`*x* `,` *min_period* `,` *max_period* `,` *num_periods*`)`

**引数**

* *x*: 数値の配列である動的配列スカラー式(通常は[、系列の作成](make-seriesoperator.md)または[make_list](makelist-aggfunction.md)演算子の結果の出力です。
* *min_period*:`real`検索する最小期間を指定する数値。
* *max_period*:`real`検索する最大期間を指定する数値。
* *num_periods*:`long`最大必要な期間数を指定する数値。 これは出力動的配列の長さになります。

> [!IMPORTANT]
> * このアルゴリズムでは、少なくとも 4 つのポイントと、系列の長さの最大半分を含む期間を検出できます。 
>
> * あなたは少し下に*min_period*を設定し、時系列で見つけると予想される期間の少し上に*max_period*する必要があります。 たとえば、時間単位の信号を使用し、毎日の>と週単位の両方の期間 (それぞれ 24 & 168) を検索する場合 *、min_period*=0.8\*24、max_period =1.2 *max_period*\*168 を設定し、これらの期間の周りに 20% のマージンを残します。
>
> * 入力時系列は、標準の、つまり定数ビンに集約されている必要があります (これは[、make-series](make-seriesoperator.md)を使用して作成された場合に常に当てはまる)。 そうでない場合、出力は意味のないものになります。


**例**

次のクエリは、1 日 2 回集計されたアプリケーションのトラフィックの月のスナップショットを埋め込みます (つまり、ビン サイズは 12 時間)。

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| render linechart 
```

:::image type="content" source="images/samples/series-periods.png" alt-text="系列期間":::

この`series_periods_detect()`シリーズで実行すると、週単位の期間 (14 ポイントの長さ) が表示されます。

```kusto
print y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, array_length(y), 1), y  
| project series_periods_detect(y, 0.0, 50.0, 2)
```

| 系列\_期間は\_\_y\_周期を検出する  | 系列\_期間は\_\_、y\_期間\_スコアを検出します。 |
|-------------|-------------------|
| [14.0, 0.0] | [0.84, 0.0]  |


サンプリングが粗すぎる(12hビンサイズ)ため、チャートにも見られる日の期間が見つからなかったので、1日の2ビンの期間は、アルゴリズムで必要な4ポイントの最小期間サイズをベローにします。