---
title: series_pearson_correlation() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでseries_pearson_correlation() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/31/2019
ms.openlocfilehash: 6454ec528e7a9e53b2feab5a7fefa1236ed80cdf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508111"
---
# <a name="series_pearson_correlation"></a>series_pearson_correlation()

2 つの数値系列入力のピアソン相関係数を計算します。

参照:[ピアソン相関係数](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient)

**構文**

`series_pearson_correlation(`*シリーズ1*`,`*シリーズ2*`)`

**引数**

* *Series1,Series2*: 相関係数を計算するための入力数値配列。 すべての引数は、同じ長さの動的配列でなければなりません。 

**戻り値**

2 つの入力間の計算されたピアソン相関係数。 数値以外の要素または存在しない要素 (サイズが異なる配列) は、結果`null`になります。

**例**

```kusto
range s1 from 1 to 5 step 1 | extend s2 = 2*s1 // Perfect correlation
| summarize s1 = make_list(s1), s2 = make_list(s2)
| extend correlation_coefficient = series_pearson_correlation(s1,s2)
```

|s1|s2|correlation_coefficient|
|---|---|---|
|[1,2,3,4,5]|[2,4,6,8,10]|1|
