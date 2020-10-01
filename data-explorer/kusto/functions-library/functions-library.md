---
title: Functions ライブラリ-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの機能を拡張するユーザー定義関数について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 8cdccf261f755a0ea7a3d6a6299aa54ce021f366
ms.sourcegitcommit: 1618cbad18f92cf0cda85cb79a5cc1aa789a2db7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2020
ms.locfileid: "91614980"
---
# <a name="functions-library"></a>関数ライブラリ

次の記事には、 [UDF (ユーザー定義関数)](../query/functions/user-defined-functions.md)のカテゴリ別の一覧が含まれています。

この記事では、ユーザー定義関数のコードを示します。  クエリに埋め込まれている let ステートメント内で使用することも、 [. create 関数](../management/create-function.md)を使用してデータベースに保存することもできます。

## <a name="machine-learning-functions"></a>Machine learning 関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[predict_fl()](predict-fl.md)|トレーニング済みの既存の機械学習モデルを使用して予測します。 |
|[predict_onnx_fl()](predict-onnx-fl.md)| ONNX 形式でトレーニング済みの既存の機械学習モデルを使用して予測します。 |

## <a name="series-processing-functions"></a>系列処理関数

|関数名     |説明                                          |
|-------------------------|--------------------------------------------------------|
|[quantize_fl()](quantize-fl.md)|量子化メトリック列。 |
|[series_fit_poly_fl()](series-fit-poly-fl.md)|回帰分析を使用して多項式を系列に合わせる。 |
|[series_moving_avg_fl()](series-moving-avg-fl.md)|系列に移動平均フィルターを適用します。 |
|[series_rolling_fl()](series-rolling-fl.md)|系列にローリング集計関数を適用します。 |
