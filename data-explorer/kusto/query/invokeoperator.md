---
title: invoke 演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの invoke 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1aca8cda34e1ee8506d5be6633cfd46fd912c6c3
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271521"
---
# <a name="invoke-operator"></a>invoke 演算子

`invoke`表形式パラメーター引数としてのソースを受け取るラムダを呼び出します。

```kusto
T | invoke foo(param1, param2)
```

**構文**

`T | invoke`*関数* `(`[*param1* `,`*param2*]`)`

**引数**

* *T*: 表形式のソース。
* *関数*: 評価するラムダ式または関数名の名前。
* *param1*, *param2* ...: 追加のラムダ引数。

**戻り値**

評価された式の結果を返します。

**メモ**

表形式引数を受け取ることができるラムダ式を宣言する方法の詳細については、「 [let ステートメント](./letstatement.md)」を参照してください。

**例**

次の例は、演算子を使用してラムダ式を呼び出す方法を示してい `invoke` ます。

<!-- csl: https://help.kusto.windows.net:443/KustoMonitoringPersistentDatabase -->
```kusto
// clipped_average(): calculates percentiles limits, and then makes another 
//                    pass over the data to calculate average with values inside the percentiles
let clipped_average = (T:(x: long), lowPercentile:double, upPercentile:double)
{
   let high = toscalar(T | summarize percentiles(x, upPercentile));
   let low = toscalar(T | summarize percentiles(x, lowPercentile));
   T 
   | where x > low and x < high
   | summarize avg(x) 
};
range x from 1 to 100 step 1
| invoke clipped_average(5, 99)
```

|avg_x|
|---|
|52|
