---
title: series_greater() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでseries_greater() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: b3f8be234cc196d55320bcace6bfca579c11a44c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508383"
---
# <a name="series_greater"></a>series_greater()

2 つの数値系列入力の要素`>`的に大きな ( ) 論理演算を計算します。

**構文**

`series_greater (`*シリーズ1*`,`*シリーズ2*`)`

**引数**

* *Series1、Series2*: 要素に対して比較される入力数値配列。 すべての引数は動的配列でなければなりません。 

**戻り値**

2 つの入力間の計算された要素的に大きなロジック演算を含むブール値の動的配列。 数値以外の要素または存在しない要素 (サイズが異なる配列) は、要素`null`値を生成します。

**例**

```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_greater_s2 = series_greater(s1, s2)
```

|s1|s2|s1_greater_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[偽、偽、真]|

**参照**

シリーズ全体の統計の比較については、以下を参照してください。
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)