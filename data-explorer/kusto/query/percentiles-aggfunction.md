---
title: 百分位 ()、パーセンタイル ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの百分位 ()、パーセンタイル () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 58d6458f0a5cf514b1acd240c9adede2022f028b
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82619113"
---
# <a name="percentile-percentiles"></a>百分位 ()、パーセンタイル ()

*Expr*によって定義された母集団の、指定された[最も近いランクの百分位](#nearest-rank-percentile)の推定値を返します。 精度は、パーセンタイル リージョンの人口密度によって異なります。 この関数は[、集計の](summarizeoperator.md)コンテキストでのみ使用できます。

* `percentiles()`はに`percentile()`似ていますが、数個の百分位値を計算します (各百分位数を個別に計算するよりも高速)。
* `percentilesw()`はに`percentilew()`似ていますが、重み付けされたパーセンタイル値を計算します (各百分位数を個別に計算するよりも高速です)。
* `percentilew()`加重`percentilesw()`パーセンタイルを計算できます。 重み付けパーセンタイルは、指定されたパーセンタイルを "重み付け" の方法で計算します。各`Weight`値は、入力で繰り返し出現したかのように扱います。

**構文**

`percentile(` *Expr* Expr`,` *百分位*の集計`)`

集計`percentiles(` *Expr* `,` *Percentile1* [`,` *Percentile2*]`)`

集計`percentiles_array(` *Expr* `,` *Percentile1* [`,` *Percentile2*]`)`

`percentiles_array(` *Expr* Expr`,` *動的配列*の集計`)`

`percentilew(` *Expr* `,` *WeightExpr* WeightExpr 百分*位*の集計`,``)`

集計`percentilesw(` *Expr* `,` *WeightExpr* WeightExpr`,` *Percentile1* [`,` *Percentile2*]`)`

集計`percentilesw_array(` *Expr* `,` *WeightExpr* WeightExpr`,` *Percentile1* [`,` *Percentile2*]`)`

`percentilesw_array(` *Expr* `,` *WeightExpr* WeightExpr 動的*配列*の集計`,``)`

**引数**

* *Expr*: 集計計算に使用される式です。
* *WeightExpr*: 集計計算の値の重みとして使用される式。
* *百分*位は、百分位を指定する2つの定数です。
* *Dynamic array*: 整数または浮動小数点数の動的配列内のパーセンタイルのリスト。

**戻り値**

グループ内の指定されたパーセンタイルの*Expr*の推定値を返します。 

**使用例**

の`Duration`値が、サンプルセットの95% より大きく、サンプルセットの5% より小さい。

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

複数の統計値を計算する場合は次のようになります。

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95), avg(Duration)
```

## <a name="weighted-percentiles"></a>重み付きパーセンタイル

時間 (期間) を測定するとします。これにより、アクションが再度完了するまでの時間がかかります。 測定値ごとに記録するのではなく、期間の各値を記録することで、100ミリ秒に丸めた値と丸められた値が出現した回数 (BucketSize) を記録します。

を使用`summarize percentilesw(Duration, BucketSize, ...)`すると、指定されたパーセンタイルを "重み付け" の方法で計算することができます。これは、実際には、これらのレコードを具体化する必要がないように、入力に BucketSize 回繰り返されます。

例: 顧客には、ミリ秒`{ 1, 1, 2, 2, 2, 5, 7, 7, 12, 12, 15, 15, 15, 18, 21, 22, 26, 35 }`単位の待機時間値のセットがあります。

帯域幅とストレージを削減するには、次のバケット`{ 10, 20, 30, 40, 50, 100 }`に事前集計を実行し、各バケットのイベント数をカウントします。これにより、次の Kusto テーブルが提供されます。

:::image type="content" source="images/percentiles-aggfunction/percentilesw-table.png" alt-text="Percentilesw テーブル":::

次のように読み取ることができます。
 * 10ミリ秒バケット内の8個のイベント (サブ`{ 1, 1, 2, 2, 2, 5, 7, 7 }`セットに対応)
 * 20ミリ秒バケット内の6つのイベント (サブ`{ 12, 12, 15, 15, 15, 18 }`セットに対応)
 * 30ミリ秒バケット内の3つのイベント ( `{ 21, 22, 26 }`サブセットに対応)
 * 40ミリ秒のバケットに1つのイベント ( `{ 35 }`サブセットに対応)

この時点で、元のデータは使用できなくなり、各バケット内のイベントの数になります。 このデータからパーセンタイルを計算するには`percentilesw()` 、関数を使用します。 たとえば、50、75、および99.9 パーセンタイルの場合は、次のクエリを使用します。 

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


データが次の形式で消費され`percentiles(LatencyBucket, 50, 75, 99.9)`た場合は、上記のクエリが関数に対応していることに注意してください。

:::image type="content" source="images/percentiles-aggfunction/percentilesw-rawtable.png" alt-text="Percentilesw raw テーブル":::

## <a name="getting-multiple-percentiles-in-an-array"></a>配列内の複数のパーセンタイルを取得する

複数の列ではなく、1つの動的列に配列として複数のパーセンタイルを取得できます。 

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, 5, 25, 50, 75, 95), avg(Duration)
```

:::image type="content" source="images/percentiles-aggfunction/percentiles-array-result.png" alt-text="パーセンタイル配列の結果":::

同様に、を使用して、重み付けパーセンタイルを動的配列として返すこともできます。`percentilesw_array`

および`percentilesw_array`の`percentiles_array`パーセンタイルは、整数または浮動小数点数の動的配列で指定できます。 配列は定数である必要がありますが、リテラルである必要はありません。

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, dynamic([5, 25, 50, 75, 95])), avg(Duration)
```

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, range(0, 100, 5)), avg(Duration)
```

## <a name="nearest-rank-percentile"></a>最も近い順位の百分位数
順序付けられた値のリストの*p*番目の百分位 (0 < *P* <= 100) は、データの*p* % がその値以下である ([パーセンタイルの Wikipedia の記事から](https://en.wikipedia.org/wiki/Percentile#The_Nearest_Rank_method)) リスト内の最小値です。

母集団の最小メンバーになるように*0*番目のパーセンタイルを定義します。

>[!NOTE]
> 計算のおおよその性質を考えると、実際に返される値は母集団のメンバーではない可能性があります。
> 最も近い順位の定義は、 *P*= 50 が[中央値の interpolative 定義](https://en.wikipedia.org/wiki/Median)に準拠していないことを意味します。 特定のアプリケーションでこの不一致の有意性を評価する場合は、母集団のサイズと[推定エラー](#estimation-error-in-percentiles)を考慮する必要があります。

## <a name="estimation-error-in-percentiles"></a>パーセンタイル単位の推定エラー

パーセンタイル集計では、 [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf)を使用して概算値を算出できます。 

>[!NOTE]
> * 推定エラーの限度は、要求されたパーセンタイルの値によって異なります。 最適な精度は [0.. 100] スケールの末尾です。 パーセンタイル0と100は、分布の最小値と最大値です。 精度はスケールの中央に向かって徐々に低下します。 中央値は最悪、1% で上限になります。 
> * エラー限度は、値ではなく、ランクで観測されます。 百分位 (X, 50) から Xm の値が返されたとします。 推定では、49% 以上 X の値の51% が Xm 以下であることが保証されます。 Xm と X の実際の中央値の差に理論的な制限はありません。
> * 推定によって正確な値が得られる場合もありますが、その場合に定義する信頼できる条件はありません。
