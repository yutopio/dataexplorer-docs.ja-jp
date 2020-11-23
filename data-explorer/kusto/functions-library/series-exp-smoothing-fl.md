---
title: series_exp_smoothing_fl ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_exp_smoothing_fl () ユーザー定義関数について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 11/22/2020
ms.openlocfilehash: 373dd34145a644fe14ea4c077a8c55ddf428ba6d
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324930"
---
# <a name="series_exp_smoothing_fl"></a>series_exp_smoothing_fl ()

系列に基本的な指数平滑フィルターを適用します。

関数は、 `series_exp_smoothing_fl()` 入力として動的な数値配列を含む式を受け取り、 [基本的な指数平滑](https://en.wikipedia.org/wiki/Exponential_smoothing#Basic_(simple)_exponential_smoothing_(Holt_linear)) フィルターを適用します。

> [!NOTE]
> この関数は、 [UDF (ユーザー定義関数)](../query/functions/user-defined-functions.md)です。 詳細については、「 [使用方法](#usage)」を参照してください。

## <a name="syntax"></a>Syntax

`series_exp_smoothing_fl(`*y_series* `, [`*アルファ*`])`
  
## <a name="arguments"></a>引数

* *y_series*: 数値の動的配列セル。
* *α*: [0-1] の範囲の任意の実数値。最後のポイントの重みと前の点の重み () を指定します。 `1-alpha` 既定値は0.5 です。

## <a name="usage"></a>使用

`series_exp_smoothing_fl()` はユーザー定義関数です。 クエリにコードを埋め込むか、データベースにインストールすることができます。 使用方法には、アドホックと永続的な2つの方法があります。 例については、以下のタブを参照してください。

# <a name="ad-hoc"></a>[アドホック](#tab/adhoc)

アドホック使用のために、 [let ステートメント](../query/letstatement.md)を使用してコードを埋め込みます。 アクセス許可は必要ありません。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_exp_smoothing_fl = (y_series:dynamic, alpha:double=0.5)
{
    series_iir(y_series, pack_array(alpha), pack_array(1, alpha-1))
}
;
range x from 1 to 50 step 1
| extend y = x % 10
| summarize x = make_list(x), y = make_list(y)
| extend exp_smooth_y = series_exp_smoothing_fl(y, 0.4) 
| render linechart
```

# <a name="persistent"></a>[永続的](#tab/persistent)

永続的に使用するには、 [. create 関数](../management/create-function.md)を使用します。 関数を作成するには、 [データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

### <a name="one-time-installation"></a>1 回限りのインストール

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Basic exponential smoothing for a series")
series_exp_smoothing_fl(y_series:dynamic, alpha:double=0.5)
{
    series_iir(y_series, pack_array(alpha), pack_array(1, alpha-1))
}
```

### <a name="usage"></a>使用

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 50 step 1
| extend y = x % 10
| summarize x = make_list(x), y = make_list(y)
| extend exp_smooth_y = series_exp_smoothing_fl(y, 0.4) 
| render linechart
```

---

:::image type="content" source="images/series-exp-smoothing-fl/exp-smoothing-demo.png" alt-text="人工系列の指数平滑を示すグラフ" border="false":::
