---
title: dcount() (集計関数) - Azure Data Explorer
description: この記事では、Azure Data Explorer のdcount() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 947ab0af6a5aaa98bb07b08005b940fdf2ce6ae5
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513184"
---
# <a name="dcount-aggregation-function"></a>dcount() (集計関数)

要約グループ内のスカラー式によって取得された個別の値の数の推定値を返します。

> [!NOTE]
> `dcount()` 集計関数は、主に大規模なセットのカーディナリティを推定するために役立ちます。 精度と引き換えにパフォーマンスを向上させており、実行ごとに異なる結果が返る可能性があります。 入力の順序が出力に影響を与える可能性があります。

## <a name="syntax"></a>構文

... `|` `summarize` `dcount` `(`*`Expr`*[, *`Accuracy`*]`)` ...

## <a name="arguments"></a>引数

* *Expr*:個別の値がカウントされるスカラー式。
* *Accuracy*:要求される推定の精度を定義する省略可能な `int` リテラル。 サポートされる値については、以下を参照してください。 指定されない場合は、既定値の `1` が使用されます。

## <a name="returns"></a>戻り値

グループ内の *`Expr`* の個別の値の概数を返します。

## <a name="example"></a>例

```kusto
PageViewLog | summarize countries=dcount(country) by continent
```

:::image type="content" source="images/dcount-aggfunction/dcount.png" alt-text="dcount":::

`G` 別にグループ化された `V` の個別の値の正確な数を取得します。

```kusto
T | summarize by V, G | summarize count() by G
```

`V` の個別の値に `G` の個別の値の数が乗算されるため、この計算には大量の内部メモリが必要です。
それによって、メモリ エラーや実行時間が長くなる可能性があります。 
`dcount()` には、高速で信頼性の高い代替手段があります。

```kusto
T | summarize dcount(B) by G | count
```

## <a name="estimation-accuracy"></a>推定精度

`dcount()` 集計関数では、セット カーディナリティの確率的推定を行う [HyperLogLog (HLL) アルゴリズム](https://en.wikipedia.org/wiki/HyperLogLog) の異形が使用されます。 アルゴリズムには、メモリ サイズごとの精度と実行時間のバランスを取るために使用できる "ノブ" が用意されています。

|精度|エラー (%)|エントリ数   |
|--------|---------|--------------|
|       0|      1.6|2<sup>12</sup>|
|       1|      0.8|2<sup>14</sup>|
|       2|      0.4|2<sup>16</sup>|
|       3|     0.28|2<sup>17</sup>|
|       4|      0.2|2<sup>18</sup>|

> [!NOTE]
> "エントリ数" 列は、HLL 実装における 1 バイト カウンターの数です。

セット カーディナリティが十分に小さい場合、アルゴリズムには完璧なカウント (ゼロ エラー) を行うためのいくつかの条件が含まれています。
* 精度レベルが `1` の場合は 1,000 個の値を返す
* 精度レベルが `2` の場合は 8,000 個の値を返す

誤り限界は確率的であり、理論限界ではありません。 値は、誤差分布の標準偏差 (シグマ) であり、推定量 の 99.7% は 3 x シグマ未満の相対エラーになります。

次の図は、サポートされているすべての精度設定の相対的な推定誤差の確率分布関数をパーセントで示したものです。

:::image type="content" border="false" source="images/dcount-aggfunction/hll-error-distribution.png" alt-text="HLL 誤差分布":::
