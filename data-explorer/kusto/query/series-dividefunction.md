---
title: series_divide ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_divide () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7d5bdba030687c17c355eb72ce2fc9c358c10ebd
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372841"
---
# <a name="series_divide"></a>series_divide()

2つの数値系列入力の要素ごとの除算を計算します。

**構文**

`series_divide(`*series1* `,`*series2*`)`

**引数**

* *series1, series2*: 入力数値配列。最初の要素は、2番目のを使用して動的配列の結果に分割されます。 すべての引数は動的配列である必要があります。 

**戻り値**

2つの入力間の計算された要素ごとの除算演算の動的配列。 数値以外の要素または存在しない要素 (異なるサイズの配列) は、 `null` 要素の値を生成します。

注: 入力が整数の場合でも、結果系列は double 型になります。 0による除算は、0による double 除算に従います (たとえば、2/0 は double (+ inf) を生成します)。

**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_divide_s2 = series_divide(s1, s2)
```

|s1         |s2|        s1_divide_s2|
|---|---|---|
|[1, 2, 4]    |[4, 2, 1]|   [0.25、1.0、4.0]|
|[2, 4, 8]    |[8, 4, 2]|   [0.25、1.0、4.0]|
|[3、6、12]   |[12, 6, 3]|  [0.25、1.0、4.0]|
