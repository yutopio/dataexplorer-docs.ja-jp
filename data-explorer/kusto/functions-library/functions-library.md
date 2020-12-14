---
title: Functions ライブラリ-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの機能を拡張するユーザー定義関数について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: b7e066133817184664e37aec52a562525afa9504
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97388955"
---
# <a name="functions-library"></a>関数ライブラリ

次の記事には、 [UDF (ユーザー定義関数)](../query/functions/user-defined-functions.md)のカテゴリ別の一覧が含まれています。

この記事では、ユーザー定義関数のコードを示します。  クエリに埋め込まれている let ステートメント内で使用することも、を使用してデータベースに保存することもでき [`.create function`](../management/create-function.md) ます。

## <a name="machine-learning-functions"></a>Machine learning 関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[kmeans_fl()](kmeans-fl.md)|K-のアルゴリズムを使用してクラスターを最適化します。 |
|[predict_fl()](predict-fl.md)|トレーニング済みの既存の機械学習モデルを使用して予測します。 |
|[predict_onnx_fl()](predict-onnx-fl.md)| ONNX 形式でトレーニング済みの既存の機械学習モデルを使用して予測します。 |

## <a name="promql-functions"></a>PromQL 関数

次のセクションでは、一般的な [Promql](https://prometheus.io/docs/prometheus/latest/querying/basics/) 関数について説明します。 これらの関数は、 [Prometheus](https://prometheus.io/) 監視システムによって取り込まれたされた Azure データエクスプローラーのメトリックの分析に使用できます。 すべての関数は、Azure データエクスプローラーのメトリックが [Prometheus データモデル](https://prometheus.io/docs/concepts/data_model/)を使用して構成されていることを前提としています。


|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[series_metric_fl ()](series-metric-fl.md)|Prometheus データモデルに格納されている時系列を選択して取得します。 |
|[series_rate_fl ()](series-rate-fl.md)|カウンターメトリックの1秒あたりの増加率の平均値を計算します。 |

## <a name="series-processing-functions"></a>系列処理関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_fl()](quantize-fl.md)|量子化メトリック列。 |
|[series_dot_product_fl()](series-dot-product-fl.md)|2つの数値ベクトルのドット積を計算します。 |
|[series_downsample_fl()](series-downsample-fl.md)|タイムシリーズを整数係数でダウンサンプリングします。 |
|[series_exp_smoothing_fl()](series-exp-smoothing-fl.md)|系列に基本的な指数平滑フィルターを適用します。 |
|[series_fit_lowess_fl()](series-fit-lowess-fl.md)|LOWESS メソッドを使用して、現地の多項式を系列に適合させる。 |
|[series_fit_poly_fl()](series-fit-poly-fl.md)|回帰分析を使用して多項式を系列に適合させる。 |
|[series_moving_avg_fl()](series-moving-avg-fl.md)|系列に移動平均フィルターを適用します。 |
|[series_rolling_fl()](series-rolling-fl.md)|系列にローリング集計関数を適用します。 |
