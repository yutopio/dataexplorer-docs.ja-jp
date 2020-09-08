---
title: series_moving_avg_udf ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_moving_avg_udf () ユーザー定義関数について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 526aa1c505dafa681665cccb0c8a8e1d56f80def
ms.sourcegitcommit: f689547c0f77b1b8bfa50a19a4518cbbc6d408e5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2020
ms.locfileid: "89557946"
---
# <a name="series_moving_avg_udf"></a>series_moving_avg_udf ()

系列に移動平均フィルターを適用します。

関数は、 `series_moving_avg_udf()` 入力として動的な数値配列を含む式を受け取り、 [単純な移動平均](https://en.wikipedia.org/wiki/Moving_average#Simple_moving_average) フィルターを適用します。

> [!NOTE]
> この関数は、 [UDF (ユーザー定義関数)](../query/functions/user-defined-functions.md)です。詳細については、「 [使用方法](#usage)」を参照してください。

## <a name="syntax"></a>構文

`series_moving_avg_udf(`*y_series* `,`*n* `, [`*中央*`])`
  
## <a name="arguments"></a>引数

* *y_series*: 数値の動的配列セル。
* *n*: 移動平均フィルターの幅。
* *center*: 移動平均が次のいずれかのオプションであるかどうかを示す省略可能なブール値。
    * 現在のポイントの前後のウィンドウで対称的に適用されます。 
    * 現在のポイントから後方にウィンドウに適用されます。 <br>
    既定では、 *center* は False です。

## <a name="usage"></a>使用法

* `series_moving_avg_udf()` はユーザー定義関数です。 クエリにコードを埋め込むか、データベースにインストールすることができます。
    * アドホック使用のために、 [let ステートメント](../query/letstatement.md)を使用してコードを埋め込みます。 アクセス許可は必要ありません。
    * 繰り返し使用する場合は、 [. create function](../management/create-function.md)を使用して関数を永続化します。 <br>
        関数を作成するには、 [データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

# <a name="ad-hoc-usage"></a>[アドホック使用](#tab/adhoc)

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_moving_avg_udf = (y_series:dynamic, n:int, center:bool=false)
{
    series_fir(y_series, repeat(1, n), true, center)
}
;
//
//  Moving average of 5 bins
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend num_ma=series_moving_avg_udf(num, 5, True)
| render timechart 
```

# <a name="persistent-usage"></a>[永続的な使用](#tab/persistent)

* **1 回限りのインストール**
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Calculate moving average of specified width")
series_moving_avg_udf(y_series:dynamic, n:int, center:bool=false)
{
    series_fir(y_series, repeat(1, n), true, center)
}
```

* **使用方法**
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  Moving average of 5 bins
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend num_ma=series_moving_avg_udf(num, 5, True)
| render timechart 
```

---

:::image type="content" source="images/series-moving-avg-udf/moving-average-five-bins.png" alt-text="5ビンの移動平均を示すグラフ" border="false":::
