---
title: パーセンタイル()、パーセンタイル() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでパーセンタイル()、パーセンタイル() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: ecbb56305cfc43033ca172071f48b25768de6d2f
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663653"
---
# <a name="percentile-percentiles"></a>パーセンタイル()、パーセンタイル()

*Expr*で定義された母集団[の、指定された最も近いランクの百分位](#nearest-rank-percentile)数の推定値を返します。 精度は、パーセンタイル リージョンの人口密度によって異なります。 この関数は集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

* `percentiles()`は`percentile()`と同様ですが、複数の百分位数を計算します (各百分位数を個別に計算するよりも高速です)。
* `percentilesw()`は`percentilew()`と同様ですが、複数の重み付けされた百分位数を計算します(各百分位を個別に計算するよりも高速です)。
* `percentilew()`加`percentilesw()`重百分位数の計算が可能です。 加重百分位数は、指定された百分位数を「加重」の方法で計算し、各値`Weight`を入力で繰り返したかのように処理します。

**構文**

`percentile(`*エクスプル*`,`*パーセンタイル*の要約`)`

`percentiles(`*エクスプル*`,`*パーセンタイル1* `,` [*パーセンタイル2*] を要約する`)`

`percentiles_array(`*エクスプル*`,`*パーセンタイル1* `,` [*パーセンタイル2*] を要約する`)`

`percentiles_array(` *Expr* `,` *動的配列*の概要`)`

`percentilew(`*エクスプル*`,`*ウェイトExpr*`,`*パーセンタイル*の要約`)`

`percentilesw(`*エクスプル*`,`*ウェイトExpr* `,` *パーセンタイル1* [`,` *パーセンタイル 2*] を要約する`)`

`percentilesw_array(`*エクスプル*`,`*ウェイトExpr* `,` *パーセンタイル1* [`,` *パーセンタイル 2*] を要約する`)`

`percentilesw_array(`*エクスプル*`,`*ウェイトExpr*`,`*動的配列*の要約`)`

**引数**

* *Expr*: 集計の計算に使用する式。
* *WeightExpr*: 集計計算の値の重みとして使用される式。
* *百分位数*は、百分位数を指定する二重定数です。
* *動的配列*: 整数または浮動小数点数の動的配列内の百分位数のリスト。

**戻り値**

グループ内の指定された百分位数の*Expr*の推定値を返します。 

**使用例**

`Duration`この値は、サンプル セットの 95% より大きく、サンプル セットの 5% 未満です。

```kusto
CallDetailRecords | summarize percentile(Duration, 95) by continent
```

同時に5、50(中央値)と95を計算します。

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95) by continent
```

:::image type="content" source="images/aggregations/percentiles.png" alt-text="パーセン タイル":::

結果は、ヨーロッパでは、呼び出しの 5% が 11.55s より短く、50% のコールが 3 分、18.46 秒、および呼び出しの 95% が 40 分 48 秒未満であることを示しています。

複数の統計値を計算する場合は次のようになります。

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95), avg(Duration)
```

## <a name="weighted-percentiles"></a>重み付きパーセンタイル

繰り返し完了するアクションを実行する時間 (期間) を測定すると仮定します。 計測値をすべて記録するのではなく、100 ミリ秒に丸めた値と、その丸められた値が表示された回数 (BucketSize) の各値を記録して、それらを凝縮します。

指定された百`summarize percentilesw(Duration, BucketSize, ...)`分位数を「加重」の方法で計算するために使用できます - 実際にそれらのレコードを具体化することなく、入力でBucketSize時間を繰り返したかのようにDurationの各値を扱うことができます。

例: 顧客は、ミリ秒単位の一連の遅延値`{ 1, 1, 2, 2, 2, 5, 7, 7, 12, 12, 15, 15, 15, 18, 21, 22, 26, 35 }`を持っています: 。

帯域幅とストレージを削減するには、次のバケットに事前集約を実行します`{ 10, 20, 30, 40, 50, 100 }`。

:::image type="content" source="images/aggregations/percentilesw-table.png" alt-text="パーセンタイルテーブル":::

次のように読み取ることができます。
 * 10ms バケット内の 8 つのイベント`{ 1, 1, 2, 2, 2, 5, 7, 7 }`(サブセットに対応)
 * 20ms バケット内の 6 つのイベント`{ 12, 12, 15, 15, 15, 18 }`(サブセットに対応)
 * 30ms バケット内の 3 つのイベント`{ 21, 22, 26 }`(サブセットに対応)
 * 40ms バケット内の 1 つのイベント`{ 35 }`(サブセットに対応)

この時点で元のデータは使用できなくなり、各バケット内のイベント数が表示されます。 このデータから百分位数を計算するには、`percentilesw()`関数を使用します。 たとえば、50、75、99.9 パーセンタイルの場合、次のクエリを使用します。 

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

:::image type="content" source="images/aggregations/percentilesw-result.PNG" alt-text="パーセンタイルの結果":::

データが次の形式に使用された場合、`percentiles(LatencyBucket, 50, 75, 99.9)`上記のクエリは関数に対応しています。

:::image type="content" source="images/aggregations/percentilesw-rawtable.png" alt-text="パーセンタイルスロウテーブル":::

## <a name="getting-multiple-percentiles-in-an-array"></a>配列内の複数の百分位数の取得

複数の百分位数は、複数の列ではなく、単一の動的列の配列として取得できます。 

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, 5, 25, 50, 75, 95), avg(Duration)
```

:::image type="content" source="images/aggregations/percentiles-array-result.png" alt-text="パーセンタイル配列の結果":::

同様に、重み付けされた百分位数は動的配列として返すことができます。`percentilesw_array`

百分位数`percentiles_array`と`percentilesw_array`整数または浮動小数点数の動的配列で指定できます。 配列は定数である必要がありますが、リテラルである必要はありません。

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, dynamic([5, 25, 50, 75, 95])), avg(Duration)
```

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, range(0, 100, 5)), avg(Duration)
```

## <a name="nearest-rank-percentile"></a>最も近いランクのパーセンタイル
*順序*付き値のリストのP-1パーセンタイル(0<P <= 100)は、データの*P*パーセントがその値以下になるようにリストの中で最も小さい値です([百分位数に関するウィキペディアの記事から](https://en.wikipedia.org/wiki/Percentile#The_Nearest_Rank_method))。 *P*

母集団の最小メンバーとなる*ように 0*番目の百分位数を定義します。

>[!NOTE]
> 計算の近似的性質を考えると、実際の戻り値は母集団のメンバーではない可能性があります。
> 最も近いランクの定義は *、P*=50 が[中央値の補間定義](https://en.wikipedia.org/wiki/Median)に準拠していないことを意味します。 特定のアプリケーションに対してこの不一致の有意性を評価する場合、母集団のサイズと[推定誤差](#estimation-error-in-percentiles)を考慮する必要があります。

## <a name="estimation-error-in-percentiles"></a>パーセンタイル単位の推定エラー

パーセンタイル集計では、 [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf)を使用して概算値を算出できます。 

>[!NOTE]
> * 推定エラーの限度は、要求されたパーセンタイルの値によって異なります。 最も正確な精度は[0..100]スケールの端にあります。 百分位数 0 と 100 は、分布の正確な最小値と最大値です。 精度はスケールの中央に向かって徐々に低下します。 中央値で最悪で、1%に制限されています。 
> * エラー限度は、値ではなく、ランクで観測されます。 百分位数(X, 50) が Xm の値を返したとします。 この推定では、X の値の少なくとも 49% と 51% 以上が Xm 以下であることを保証します。 Xm と実際の中央値 X の間の差に理論的な制限はありません。
> * 推定結果が正確な値になる場合がありますが、いつ当てはまるかは、信頼できる条件はありません。
