---
title: notbetween 演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの notbetween 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7dd8f04010cbc10afa34fa8b27e6be8d31feae9d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248696"
---
# <a name="not-between-operator-between"></a>not-between 演算子 (!between)

包括範囲外の入力と一致します。

```kusto
Table1 | where Num1 !between (1 .. 10)
Table1 | where Time !between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`!between` は、任意の数値、datetime、または timespan 式で操作できます。
 
## <a name="syntax"></a>構文

*T* `|` `where` *expr* `!between` `(` *leftRange* ` .. ` *rightRange*`)`   
 
*Expr*式が datetime の場合-別の構文の砂糖構文が提供されます。

*T* `|` `where` *expr* `!between` `(` *leftRangeDateTime* ` .. ` *rightRangeTimespan*`)`   

## <a name="arguments"></a>引数

* *T* -レコードが照合される表形式の入力。
* *expr* -フィルター処理する式。
* *leftRange* -左の範囲の式 (包括)。
* *rightRange* -右側の範囲の式。

## <a name="returns"></a>戻り値

の述語*T* (*expr*  <  *leftRange*または*expr*  >  *rightRange*) がに評価される T 内の行 `true` 。

## <a name="examples"></a>例  

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
