---
title: series_less_equals() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでseries_less_equals() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 2dc03b1f1e24aaef4a6a006d72aeb980753e871e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508281"
---
# <a name="series_less_equals"></a>series_less_equals()

2 つの数値系列入力の要素の小`<=`さまたは等しい ( ) ロジック演算を計算します。

**構文**

`series_less_equals (`*シリーズ1*`,`*シリーズ2*`)`

**引数**

* *Series1、Series2*: 要素に対して比較される入力数値配列。 すべての引数は動的配列でなければなりません。 

**戻り値**

2 つの入力間の計算された要素単位の小数または等しいロジック演算を含むブール値の動的配列。 数値以外の要素または存在しない要素 (サイズが異なる配列) は、要素`null`値を生成します。

**例**

```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_less_equals_s2 = series_less_equals(s1, s2)
```

|s1|s2|s1_less_equals_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[真、真、偽]|

**参照**

シリーズ全体の統計の比較については、以下を参照してください。
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)