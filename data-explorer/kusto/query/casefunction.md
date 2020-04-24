---
title: ケース() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの case() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 479c4a99d410a2df7a608531914d7dccfc555e7e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517274"
---
# <a name="case"></a>case()

述語のリストを評価し、述部が満たされた最初の結果式を返します。

どちらの述部も戻っていない`true`場合は、最後の式 ( )`else`の結果が返されます。
すべての奇数引数 (カウントは 1 から始まる)`boolean`は、値に評価される式でなければなりません。
すべての偶数引数`then`(s) と最後の引数`else`() は同じ型でなければなりません。

**構文**

`case(`*predicate_1then_1* `,` *then_1*、 *predicate_2* `,` *then_2*、 *predicate_3* `,` *then_3* *、*`)`

**引数**

* *predicate_i*:`boolean`値に評価される式。
* *then_i*: 評価を受け取り、その値が関数から返される*式predicate_i最初*の述語が`true`に評価される場合。
* *else*: 評価を受け取る式で、その値は`true`*、どちらも評価predicate_i*が .

**戻り値**

*predicate_i*が`true`に評価される最初の*then_i*の値、または述部のどちらも満たされない場合は*else*の値。

**例**

```kusto
range Size from 1 to 15 step 2
| extend bucket = case(Size <= 3, "Small", 
                       Size <= 10, "Medium", 
                       "Large")
```

|サイズ|bucket|
|---|---|
|1|Small|
|3|Small|
|5|Medium|
|7|Medium|
|9|Medium|
|11|Large|
|13|Large|
|15|Large|
