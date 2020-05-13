---
title: series_stats ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_stats () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: 3fe88a5d53faaca4512d614d3e62204ac26e6fc5
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372445"
---
# <a name="series_stats"></a>series_stats()

`series_stats()`複数の列に含まれる系列の統計を返します。  

関数は、 `series_stats()` 入力として動的な数値配列を含む列を受け取り、次の列を計算します。
* `min`: 入力配列の最小値
* `min_idx`: 入力配列内の最小値の最初の位置
* `max`: 入力配列の最大値
* `max_idx`: 入力配列内の最大値の最初の位置
* `avg`: 入力配列の平均値
* `variance`: 入力配列のサンプル分散
* `stdev`: 入力配列の標準偏差のサンプル

> [!NOTE] 
> この関数は複数の列を返します。そのため、別の関数の引数として使用することはできません。

**構文**

プロジェクト `series_stats(` *x* `[,` *ignore_nonfinite* `])` または `series_stats(` *x* `)` を拡張すると、前述のすべての列が、series_stats_x_min、series_stats_x_min_idx などの名前で返されます。
 
project (m, mi) = `series_stats(` *x* `)` または extend (m, mi) = `series_stats(` *x*は `)` 、次の列を返します: m (min) と mi (min_idx)。

**引数**

* *x*: 数値の配列である動的配列のセル。 
* *ignore_nonfinite*: `false` 非有限値 (*null*、 *NaN*、 *inf*など) を無視して統計を計算するかどうかを指定するブール値 (省略可能、既定値:) フラグ。 に設定されている場合 `false` 、 `null` 配列内に非有限値があると、戻り値はになります。

**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project series_stats(x)

```

|series_stats_x_min|series_stats_x_min_idx|series_stats_x_max|series_stats_x_max_idx|series_stats_x_avg|series_stats_x_stdev|series_stats_x_variance|
|---|---|---|---|---|---|---|
|2|8|87|3|32.8|28.5036338535483|812.457142857143|
