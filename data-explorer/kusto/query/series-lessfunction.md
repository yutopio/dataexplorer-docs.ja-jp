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
ms.openlocfilehash: d6ed6f073b1b1aa93b5767c46459a7c8a7b4bf55
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103524"
---
# <a name="series_less"></a>series_less()

`<`2 つの数値系列入力の要素ごとの less () 論理演算を計算します。

## <a name="syntax"></a>構文

`series_less (`*Series1* `,`*Series2*`)`

## <a name="arguments"></a>引数

* *Series1, Series2*: 要素ごとに比較する数値配列を入力します。 すべての引数は動的配列である必要があります。 

## <a name="returns"></a>戻り値

2つの入力間の計算された要素ごとの低いロジック操作を含むブール値の動的配列。 数値以外の要素または存在しない要素 (異なるサイズの配列) は、 `null` 要素の値を生成します。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_less_s2 = series_less(s1, s2)
```

|s1|s2|s1_less_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[true、false、false]|

## <a name="see-also"></a>関連項目

系列統計の比較全体については、以下を参照してください。
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
