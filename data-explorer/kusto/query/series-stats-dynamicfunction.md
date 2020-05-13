---
title: series_stats_dynamic ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_stats_dynamic () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: 87cee5244fb1276733d4cf44d0477cc3351b947c
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372450"
---
# <a name="series_stats_dynamic"></a>series_stats_dynamic()

動的オブジェクトの系列の統計を返します。  

関数は、 `series_stats_dynamic()` 入力として動的な数値配列を含む列を受け取り、次の内容の動的な値を生成します。
* `min`: 入力配列の最小値
* `min_idx`: 入力配列内の最小値の最初の位置
* `max`: 入力配列の最大値
* `max_idx`: 入力配列内の最大値の最初の位置
* `avg`: 入力配列の平均値
* `variance`: 入力配列のサンプル分散
* `stdev`: 入力配列の標準偏差のサンプル

**構文**

`series_stats_dynamic(`*x* `[,` *ignore_nonfinite*`])`

**引数**

* *x*: 数値の配列である動的配列のセル。 
* *ignore_nonfinite*: `false` 非有限値 (*null*、 *NaN*、 *inf*など) を無視して統計を計算するかどうかを指定するブール値 (省略可能、既定値:) フラグ。 がに設定されている場合 `false` 、 `null` 非有限値が配列内に存在する場合は、が返されます。

**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project stats=series_stats_dynamic(x)
```

|stats
|---|
|{"min": 2.0、"min_idx": 8、"max": 87.0、"max_idx": 3、"avg": 32.8、"stdev": 28.503633853548269、"variance": 812.45714285714291}
