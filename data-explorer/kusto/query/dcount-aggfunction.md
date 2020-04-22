---
title: dcount() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの dcount() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6f1df8c93d21b73be3753468708a119177d4d602
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030415"
---
# <a name="dcount-aggregation-function"></a>dcount() (集計関数)

サマリー グループ内のスカラー式によって取得された個別値の数の推定値を返します。

**構文**

...`|` `,` *Accuracy*`)` *Expr* Expr [ 精度 ] .. `summarize` `dcount` `(`

**引数**

* *Expr*: 個別の値がカウントされるスカラー式。
* *精度*: 要求`int`された推定精度を定義するオプションのリテラル。 サポートされている値については、以下を参照してください。 指定されていない場合は、既定値`1`が使用されます。

**戻り値**

グループ内にある*式*の個別の値の概数を返します。

**例**

```kusto
PageViewLog | summarize countries=dcount(country) by continent
```

:::image type="content" source="images/dcount-aggfunction/dcount.png" alt-text="D カウント":::

**メモ**

集計`dcount()`関数は、巨大な集合のカーディナリティを推定する場合に主に役立ちます。 パフォーマンスを正確にトレードするため、実行によって異なる結果が返される可能性があります (入力の順序は、その出力に影響を与える可能性があります)。

によってグループ化された個別の値の`V`正確なカウントを`G`取得するには

```kusto
T | summarize by V, G | summarize count() by G
```

この計算では、個別の`V`値に異なる値の数が乗算されるため、多くの内部メモリが`G`必要になります。そのため、メモリ エラーや実行時間が長くなる可能性があります。 `dcount()`高速で信頼性の高い代替手段を提供します。

```kusto
T | summarize dcount(B) by G | count
```

## <a name="estimation-accuracy"></a>推定精度

集合`dcount()`関数は、集合カーディナリティーの確率的推定を行う[HyperLogLog (HLL) アルゴリズム](https://en.wikipedia.org/wiki/HyperLogLog)のバリアントを使用します。 アルゴリズムは、精度と実行時間/メモリサイズのバランスをとるために使用できる「ノブ」を提供します。

|精度|エラー (%)|エントリ数   |
|--------|---------|--------------|
|       0|      1.6|2<sup>12</sup>|
|       1|      0.8|2<sup>14</sup>|
|       2|      0.4|2<sup>16</sup>|
|       3|     0.28|2<sup>17</sup>|
|       4|      0.2|2<sup>18</sup>|

> [!NOTE]
> 「エントリカウント」列は、HLL 実装の 1 バイトのカウンターの数です。

アルゴリズムには、セットカーディナリティが十分に小さい場合に完全なカウント(ゼロエラー)を実行するためのいくつかの規定が含まれています(精度レベル`1`が、精度レベルが、精度レベルが、である場合は`2`8000値)。

誤差の範囲は確率的であり、理論上の境界ではありません。 この値は誤差分布の標準偏差(シグマ)であり、推定の99.7%は3倍のシグマ以下の相対誤差を有する。

次に、サポートされているすべての精度設定の相対推定誤差 (パーセント) の確率分布関数を示します。

:::image type="content" border="false" source="images/dcount-aggfunction/hll-error-distribution.png" alt-text="hll エラーの分布":::
