---
title: Functions ライブラリ-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの機能を拡張するユーザー定義関数について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 66f8da1655fada7429fcd3087810904bd184546c
ms.sourcegitcommit: 993bc7b69096ab5516d3c650b9df97a1f419457b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614520"
---
# <a name="functions-library"></a>Functions ライブラリ

次の記事には、ユーザー定義関数のカテゴリ別の一覧が含まれています。

## <a name="machine-learning-functions"></a>Machine learning 関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[predict_lf ()](predict-lf.md)|トレーニング済みの既存の機械学習モデルを使用して予測します。 |
|[predict_onnx_lf ()](predict-onnx-lf.md)| ONNX 形式でトレーニング済みの既存の機械学習モデルを使用して予測します。 |

## <a name="series-processing-functions"></a>系列処理関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_lf ()](quantize-lf.md)|量子化メトリック列。 |
|[series_fit_poly_lf ()](series-fit-poly-lf.md)|回帰分析を使用して多項式を系列に合わせる。 |
|[series_moving_avg_lf ()](series-moving-avg-lf.md)|系列に移動平均フィルターを適用します。 |
|[series_rolling_lf ()](series-rolling-lf.md)|系列にローリング集計関数を適用します。 |
