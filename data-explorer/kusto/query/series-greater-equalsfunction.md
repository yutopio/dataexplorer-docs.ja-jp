---
title: series_greater_equals ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_greater_equals () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 6c394c33af25e1891131c7ca2a47359f3cdcd059
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372657"
---
# <a name="series_greater_equals"></a>series_greater_equals()

`>=`2 つの数値系列入力の要素ごとのより大きいまたは等しい () 論理演算を計算します。

**構文**

`series_greater_equals (`*Series1* `,`*Series2*`)`

**引数**

* *Series1, Series2*: 要素ごとに比較する数値配列を入力します。 すべての引数は動的配列である必要があります。 

**戻り値**

2つの入力間の論理演算のうち、計算された要素ごとの or を含むブール値の動的配列。 数値以外の要素または存在しない要素 (異なるサイズの配列) は、 `null` 要素の値を生成します。

**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_greater_equals_s2 = series_greater_equals(s1, s2)
```

|s1|s2|s1_greater_equals_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[false、true、true]|

**参照**

系列統計の比較全体については、以下を参照してください。
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
