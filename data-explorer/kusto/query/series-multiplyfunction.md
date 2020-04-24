---
title: series_multiply() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの series_multiply() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: fa000d1058730e0232790e7f0e3976fa203519c0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508213"
---
# <a name="series_multiply"></a>series_multiply()

2 つの数値系列入力の要素ごとの乗算を計算します。

**構文**

`series_multiply(`*シリーズ1*`,`*シリーズ2*`)`

**引数**

* *series1,series2*: 動的配列の結果に要素的に乗算される入力数値配列。 すべての引数は動的配列でなければなりません。 

**戻り値**

2 つの入力間の計算された要素ごとの乗算演算の動的配列。 数値以外の要素または存在しない要素 (サイズが異なる配列) は、要素`null`値を生成します。

**例**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_multiply_s2 = series_multiply(s1, s2)
```

|s1         |s2|        s1_multiply_s2|
|---|---|---|
|[1,2,4]    |[4,2,1]|   [4,4,4]|
|[2,4,8]    |[8,4,2]|   [16,16,16]|
|[3,6,12]   |[12,6,3]|  [36,36,36]|