---
title: series_divide() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで series_divide() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 8e8b806c325da9bfce5f79ce5a5c4e5cfadaa838
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508842"
---
# <a name="series_divide"></a>series_divide()

2 つの数値系列入力の要素ごとの分割を計算します。

**構文**

`series_divide(`*シリーズ1*`,`*シリーズ2*`)`

**引数**

* *series1、series2*: 入力数値配列、要素単位で2番目の配列を動的配列の結果に分割する最初の配列。 すべての引数は動的配列でなければなりません。 

**戻り値**

2 つの入力間の計算された要素ごとの除算演算の動的配列。 数値以外の要素または存在しない要素 (サイズが異なる配列) は、要素`null`値を生成します。

注: 入力が整数であっても、結果系列は倍精度浮動小数点型です。 ゼロによる除算は、2 倍のゼロ除算 (例えば 2/0 の 2 倍(+inf)) に続きます。

**例**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_divide_s2 = series_divide(s1, s2)
```

|s1         |s2|        s1_divide_s2|
|---|---|---|
|[1,2,4]    |[4,2,1]|   [0.25,1.0,4.0]|
|[2,4,8]    |[8,4,2]|   [0.25,1.0,4.0]|
|[3,6,12]   |[12,6,3]|  [0.25,1.0,4.0]|