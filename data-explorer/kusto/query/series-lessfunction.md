---
title: series_less ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_less () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: b1f51f30825ceecfc025219f61d181c39ab0268f
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372598"
---
# <a name="series_less"></a>series_less()

`<`2 つの数値系列入力の要素ごとの less () 論理演算を計算します。

**構文**

`series_less (`*Series1* `,`*Series2*`)`

**引数**

* *Series1, Series2*: 要素ごとに比較する数値配列を入力します。 すべての引数は動的配列である必要があります。 

**戻り値**

2つの入力間の計算された要素ごとの低いロジック操作を含むブール値の動的配列。 数値以外の要素または存在しない要素 (異なるサイズの配列) は、 `null` 要素の値を生成します。

**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_less_s2 = series_less(s1, s2)
```

|s1|s2|s1_less_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[true、false、false]|

**参照**

系列統計の比較全体については、以下を参照してください。
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
