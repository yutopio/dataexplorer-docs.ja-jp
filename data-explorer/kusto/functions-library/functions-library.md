---
title: Functions ライブラリ-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの機能を拡張するユーザー定義関数について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 5b3457d52be37d4c0090db2f34c89994bc829a53
ms.sourcegitcommit: 50c799c60a3937b4c9e81a86a794bdb189df02a3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90067540"
---
# <a name="functions-library"></a>関数ライブラリ

次の記事には、ユーザー定義関数のカテゴリ別の一覧が含まれています。

## <a name="machine-learning-functions"></a>Machine learning 関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[predict_fl ()](predict-fl.md)|トレーニング済みの既存の機械学習モデルを使用して予測します。 |
|[predict_onnx_fl ()](predict-onnx-fl.md)| ONNX 形式でトレーニング済みの既存の機械学習モデルを使用して予測します。 |

## <a name="series-processing-functions"></a>系列処理関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_fl ()](quantize-fl.md)|量子化メトリック列。 |
|[series_fit_poly_fl ()](series-fit-poly-fl.md)|回帰分析を使用して多項式を系列に合わせる。 |
|[series_moving_avg_fl ()](series-moving-avg-fl.md)|系列に移動平均フィルターを適用します。 |
|[series_rolling_fl ()](series-rolling-fl.md)|系列にローリング集計関数を適用します。 |
