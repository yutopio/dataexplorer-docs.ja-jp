---
title: series_greater ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_greater () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: bd75a6aec420c6c7536e1e99217c819f701a15c0
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351456"
---
# <a name="series_greater"></a>series_greater()

`>`2 つの数値系列入力の要素ごとに大きい () 論理演算を計算します。

## <a name="syntax"></a>構文

`series_greater (`*Series1* `,`*Series2*`)`

## <a name="arguments"></a>引数

* *Series1, Series2*: 要素ごとに比較する数値配列を入力します。 すべての引数は動的配列である必要があります。 

## <a name="returns"></a>戻り値

2つの入力間の計算された要素ごとの論理演算を含むブール値の動的配列。 数値以外の要素または存在しない要素 (異なるサイズの配列) は、 `null` 要素の値を生成します。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_greater_s2 = series_greater(s1, s2)
```

|s1|s2|s1_greater_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[false、false、true]|

**参照**

系列統計の比較全体については、以下を参照してください。
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
