---
title: 呼び出し演算子 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの呼び出し演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 41f19440795f4f302352a8dda5192c5c4790ea99
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513704"
---
# <a name="invoke-operator"></a>invoke 演算子

表形式パラメーター引数としてソースを受け`invoke`取るラムダを呼び出します。

```kusto
T | invoke foo(param1, param2)
```

**構文**

`T | invoke`*機能*`(`[*パラム1*`,`*パラム2*]`)`

**引数**

* *T*: 表形式のソース。
* *function*: 評価するラムダ式または関数名の名前。
* *param1* *,param2..* : ラムダ引数を追加します。

**戻り値**

評価された式の結果を返します。

**メモ**

表形式の引数を受け取ることができるラムダ式を宣言する方法の詳細については[、let ステートメント](./letstatement.md)を参照してください。

**例**

次の例は、演算子を`invoke`使用してラムダ式を呼び出す方法を示しています。

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
