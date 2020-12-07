---
title: Functions ライブラリ-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの機能を拡張するユーザー定義関数について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: fb08edf4105c44a6be96cf6b2f314cf25887e69a
ms.sourcegitcommit: 4d5628b52b84f7564ea893f621bdf1a45113c137
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "96443991"
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
