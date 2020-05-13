---
title: notbetween 演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの notbetween 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: e951e56589824acd6dd74160c9ab61778fbce45e
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271555"
---
# <a name="between-operator"></a>! between 演算子

包括範囲外の入力と一致します。

```kusto
Table1 | where Num1 !between (1 .. 10)
Table1 | where Time !between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`!between`は、任意の数値、datetime、または timespan 式で操作できます。
 
**構文**

*T* `|` `where` *expr* `!between` `(` *leftRange* ` .. ` *rightRange*`)`   
 
*Expr*式が datetime の場合-別の構文の砂糖構文が提供されます。

*T* `|` `where` *expr* `!between` `(` *leftRangeDateTime* ` .. ` *rightRangeTimespan*`)`   

**引数**

* *T* -レコードが照合される表形式の入力。
* *expr* -フィルター処理する式。
* *leftRange* -左の範囲の式 (包括)。
* *rightRange* -rihgt の範囲 (包括的) の式。

**戻り値**

の述語*T* (*expr*  <  *leftRange*または*expr*  >  *rightRange*) がに評価される T 内の行 `true` 。

**使用例**  

**'! Between ' 演算子を使用して数値をフィルター処理しています**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 10 step 1
| where x !between (5 .. 9)
```

|x|
|---|
|1|
|2|
|3|
|4|
|10|

**' Between ' 演算子を使用した datetime をフィルター処理しています**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|Count|
|---|
|58590|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime !between (datetime(2007-07-27) .. 3d)
| count 
```

|Count|
|---|
|58590|
