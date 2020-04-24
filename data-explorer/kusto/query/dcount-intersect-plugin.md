---
title: dcount_intersectプラグイン - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーdcount_intersectプラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7771456ffa75085c79933c2e789e3d98f352b76f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516135"
---
# <a name="dcount_intersect-plugin"></a>dcount_intersectプラグイン

hll 値 ([2..16]の範囲の N) に基づいて N セット間の積集合を計算し、N 個の dcount 値を返します。

与えられたセット S<sub>1,</sub>S<sub>2</sub>, . S<sub>n</sub> - 戻り値は、次の個別のカウントを表します。  
S<sub>1,</sub>S<sub>1</sub>個 S<sub>2</sub>,  
S<sub>10s</sub> S<sub>2</sub> 20s<sub>3,</sub>  
... ,  
S<sub>1</sub> 100s<sub>22..</sub>サ・<sub>ン</sub>

    T | evaluate dcount_intersect(hll_1, hll_2, hll_3)

**構文**

*T* `| evaluate` T `dcount_intersect(` *hll_1*,`,` *hll_2*, [ *hll_3* `,` ..]`)`

**引数**

* *T*: 入力表形式の式。
* *hll_i*: [hll()](./hll-aggfunction.md)関数で計算されたセット S<sub>i</sub>の値。

**戻り値**

N 個の dcount 値を持つテーブルを返します (列列ごとに、セットの交差を表します)。
列名は s0、s1、..(n-1まで)。

**使用例**

```kusto
// Generate numbers from 1 to 100
range x from 1 to 100 step 1
| extend isEven = (x % 2 == 0), isMod3 = (x % 3 == 0), isMod5 = (x % 5 == 0)
// Calculate conditional HLL values (note that '0' is included in each of them as additional value, so we will subtract it later)
| summarize hll_even = hll(iif(isEven, x, 0), 2),
            hll_mod3 = hll(iif(isMod3, x, 0), 2),
            hll_mod5 = hll(iif(isMod5, x, 0), 2) 
// Invoke the plugin that calculates dcount intersections         
| evaluate dcount_intersect(hll_even, hll_mod3, hll_mod5)
| project evenNumbers = s0 - 1,             //                             100 / 2 = 50
          even_and_mod3 = s1 - 1,           // gcd(2,3) = 6, therefor:     100 / 6 = 16
          even_and_mod3_and_mod5 = s2 - 1   // gcd(2,3,5) is 30, therefore: 100 / 30 = 3 
```

|偶数番号|even_and_mod3|even_and_mod3_and_mod5|
|---|---|---|
|50|16|3|