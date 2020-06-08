---
title: 百分位 ()、パーセンタイル ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの百分位 ()、パーセンタイル () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: dfb957189eb0d9be552cf12b32ef57452a375c51
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512556"
---
# <a name="percentile-percentiles"></a>百分位 ()、パーセンタイル ()

によって定義された、指定された人口の[最も近い順位の百分位](#nearest-rank-percentile)の推定値を返し `*Expr*` ます。
精度は、パーセンタイル リージョンの人口密度によって異なります。 この関数は[、集計の](summarizeoperator.md)コンテキストでのみ使用できます。

* `percentiles()`はに似ていますが、複数のパーセンタイル値を計算します `percentile()` 。これは、各百分位数を個別に計算するよりも高速です。
* `percentilesw()`はに似てい `percentilew()` ますが、複数の重み付け百分位値を計算します。各百分位数を個別に計算するよりも高速です。
* `percentilew()``percentilesw()`重み付けパーセンタイルを計算できます。 重み付けパーセンタイルは、指定されたパーセンタイルを入力で繰り返しているかのように処理することで、特定のを "重み付け" の方法で計算し `weight` ます。

**構文**

`percentile(` *Expr* `,` *百分位*の集計`)`

集計 `percentiles(` *Expr* `,` *Percentile1* [ `,` *Percentile2*]`)`

集計 `percentiles_array(` *Expr* `,` *Percentile1* [ `,` *Percentile2*]`)`

`percentiles_array(` *Expr* `,` *動的配列*の集計`)`

`percentilew(` *Expr* `,` *WeightExpr* `,` *百分位*の集計`)`

集計 `percentilesw(` *Expr* `,` *WeightExpr* `,` *Percentile1* [ `,` *Percentile2*]`)`

集計 `percentilesw_array(` *Expr* `,` *WeightExpr* `,` *Percentile1* [ `,` *Percentile2*]`)`

`percentilesw_array(` *Expr* `,` *WeightExpr* `,` *動的配列*の集計`)`

**引数**

* `*Expr*`: 集計計算に使用される式。
* `*WeightExpr*`: 集計計算の値の重みとして使用される式。
* `*Percentile*`: 百分位を指定する double 定数。
* `*Dynamic array*`: 整数または浮動小数点数の動的配列内のパーセンタイルの一覧。

**戻り値**

`*Expr*`グループ内の指定されたパーセンタイルの見積もりを返します。 

**使用例**

の値が、 `Duration` サンプルセットの95% より大きく、サンプルセットの5% より小さい。

```kusto
CallDetailRecords | summarize percentile(Duration, 95) by continent
```

5、50 (中央値)、および95を同時に計算します。

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95) by continent
```

:::image type="content" source="images/percentiles-aggfunction/percentiles.png" alt-text="パーセンタイル":::

ヨーロッパでは、呼び出しの5% は 11.55 s より短い、呼び出しの50% は3分、18.46 秒、呼び出しの95% は 40 minutes 48 秒未満であることが結果に示されています。

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95), avg(Duration)
```

## <a name="weighted-percentiles"></a>重み付きパーセンタイル

時間 (期間) を繰り返し測定して、アクションが完了することを前提としています。 測定値ごとに記録するのではなく、期間の各値を100ミリ秒に丸め、丸められた値が出現した回数 (BucketSize) を記録します。

を使用し `summarize percentilesw(Duration, BucketSize, ...)` て、指定されたパーセンタイルを "重み付け" の方法で計算します。 各期間の値は、入力の BucketSize 回繰り返されたかのように処理します。実際には、これらのレコードを具体化する必要はありません。

**例**

顧客は、ミリ秒単位で待機時間値のセットを持ちます `{ 1, 1, 2, 2, 2, 5, 7, 7, 12, 12, 15, 15, 15, 18, 21, 22, 26, 35 }` 。

帯域幅と記憶域を削減するには、次のバケットに事前 `{ 10, 20, 30, 40, 50, 100 }` 集計します。 各バケット内のイベント数をカウントして、次のテーブルを生成します。

:::image type="content" source="images/percentiles-aggfunction/percentilesw-table.png" alt-text="Percentilesw テーブル":::

次の表が表示されます。
 * 10ミリ秒バケットの8個のイベント (サブセットに対応 `{ 1, 1, 2, 2, 2, 5, 7, 7 }` )
 * 20ミリ秒バケットの6つのイベント (サブセットに対応 `{ 12, 12, 15, 15, 15, 18 }` )
 * 30ミリ秒バケット内の3つのイベント (サブセットに対応 `{ 21, 22, 26 }` )
 * 40-ms バケット内の1つのイベント (サブセットに対応 `{ 35 }` )

この時点で、元のデータは使用できなくなります。 各バケット内のイベント数のみ。 このデータからパーセンタイルを計算するには、関数を使用し `percentilesw()` ます。
たとえば、50、75、および99.9 パーセンタイルの場合は、次のクエリを使用します。

```kusto
datatable (ReqCount:long, LatencyBucket:long) 
[ 
    8, 10, 
    6, 20, 
    3, 30, 
    1, 40 
]
| summarize percentilesw(LatencyBucket, ReqCount, 50, 75, 99.9) 
```

上記のクエリの結果は次のようになります。

:::image type="content" source="images/percentiles-aggfunction/percentilesw-result.png" alt-text="Percentilesw の結果" border="false":::


上記のクエリは、データが `percentiles(LatencyBucket, 50, 75, 99.9)` 次の形式に拡張されている場合に、関数に対応します。

:::image type="content" source="images/percentiles-aggfunction/percentilesw-rawtable.png" alt-text="Percentilesw raw テーブル":::

## <a name="getting-multiple-percentiles-in-an-array"></a>配列内の複数のパーセンタイルを取得する

複数のパーセンタイルは、複数の列ではなく、1つの動的列で配列として取得できます。

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, 5, 25, 50, 75, 95), avg(Duration)
```

:::image type="content" source="images/percentiles-aggfunction/percentiles-array-result.png" alt-text="パーセンタイル配列の結果":::

同様に、重み付けパーセンタイルは、を使用して動的配列として返すことができ `percentilesw_array` ます。

およびのパーセンタイルは、 `percentiles_array` `percentilesw_array` 整数または浮動小数点数の動的配列で指定できます。 配列は定数である必要がありますが、リテラルである必要はありません。

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, dynamic([5, 25, 50, 75, 95])), avg(Duration)
```

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, range(0, 100, 5)), avg(Duration)
```

## <a name="nearest-rank-percentile"></a>最も近い順位の百分位数

昇順で並べ替えられた値のリストの*p*番目の百分位 (0 < *P* <= 100) は、リストの最小値になります。 データの*p* % は、 *p*パーセンタイル値以下です ([パーセンタイルの Wikipedia の記事を](https://en.wikipedia.org/wiki/Percentile#The_Nearest_Rank_method)ご覧ください)。

母集団の最小メンバーになるように*0*番目のパーセンタイルを定義します。

>[!NOTE]
> 計算のおおよその性質を考えると、実際に返される値は母集団のメンバーではない可能性があります。
> 最も近い順位の定義は、 *P*= 50 が[中央値の interpolative 定義](https://en.wikipedia.org/wiki/Median)に準拠していないことを意味します。 特定のアプリケーションでこの不一致の有意性を評価する場合は、母集団のサイズと[推定エラー](#estimation-error-in-percentiles)を考慮する必要があります。

## <a name="estimation-error-in-percentiles"></a>パーセンタイル単位の推定エラー

パーセンタイル集計では、 [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf)を使用して概算値を算出できます。

>[!NOTE]
> * 推定エラーの限度は、要求されたパーセンタイルの値によって異なります。 最適な精度は、[0.. 100] スケールの両端にあります。 パーセンタイル0と100は、分布の最小値と最大値です。 精度はスケールの中央に向かって徐々に低下します。 中央値は最悪、1% で上限になります。
> * エラー限度は、値ではなく、ランクで観測されます。 百分位 (X, 50) から Xm の値が返されたとします。 この見積もりでは、少なくとも49% と X の値の51% が Xm 以下であることが保証されます。 Xm と X の実際の中央値の差に理論的な制限はありません。
> * 推定によって正確な値が得られる場合もありますが、その場合に定義する信頼できる条件はありません。
