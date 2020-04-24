---
title: series_subtract() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで series_subtract() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 1e984bc35192da5d61448211c49ff582f225eb19
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507941"
---
# <a name="series_subtract"></a>series_subtract()

2 つの数値系列入力の要素ごとの減算を計算します。

**構文**

`series_subtract(`*シリーズ1*`,`*シリーズ2*`)`

**引数**

* *series1、series2*: 入力数値配列、第1の配列から動的配列の結果に要素方向に減算される2番目の配列。 すべての引数は動的配列でなければなりません。 

**戻り値**

2 つの入力間の計算された要素方向の減算演算の動的配列。 数値以外の要素または存在しない要素 (サイズが異なる配列) は、要素`null`値を生成します。

**例**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_subtract_s2 = series_subtract(s1, s2)
```

|s1|s2|s1_subtract_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[-3,0,3]|
|[2,4,8]|[8,4,2]|[-6,0,6]|
|[3,6,12]|[12,6,3]|[-9,0,9]|