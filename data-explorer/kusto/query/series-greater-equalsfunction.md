---
title: series_greater_equals() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで series_greater_equals() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 21a1e4d972c06f212344a665c6b3320046ee93c8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508400"
---
# <a name="series_greater_equals"></a>series_greater_equals()

2 つの数値系列入力の要素方向の大`>=`または等しいロジック演算を計算します。

**構文**

`series_greater_equals (`*シリーズ1*`,`*シリーズ2*`)`

**引数**

* *Series1、Series2*: 要素に対して比較される入力数値配列。 すべての引数は動的配列でなければなりません。 

**戻り値**

2 つの入力間の計算された要素ごとの大または等しい論理演算を含むブール値の動的配列。 数値以外の要素または存在しない要素 (サイズが異なる配列) は、要素`null`値を生成します。

**例**

```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_greater_equals_s2 = series_greater_equals(s1, s2)
```

|s1|s2|s1_greater_equals_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[偽、真、真]|

**参照**

シリーズ全体の統計の比較については、以下を参照してください。
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)