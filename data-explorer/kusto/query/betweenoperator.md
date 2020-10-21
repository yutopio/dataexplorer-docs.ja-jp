---
title: between 演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの演算子の違いについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 15112a72c289d87f6a1f1a2b035cb13bad81acdb
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245458"
---
# <a name="between-operator"></a>between 演算子

両端を含む範囲内に入っている値が一致として出力されます。

```kusto
Table1 | where Num1 between (1 .. 10)
Table1 | where Time between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`between` は、任意の数値、datetime、または timespan 式で操作できます。
 
## <a name="syntax"></a>構文

*T* `|` `where` *expr* `between` `(` *leftRange* ` .. ` *rightRange*`)`   
 
*Expr*式が datetime の場合-別の構文の砂糖構文が提供されます。

*T* `|` `where` *expr* `between` `(` *leftRangeDateTime* ` .. ` *rightRangeTimespan*`)`   

## <a name="arguments"></a>引数

* *T* -レコードが照合される表形式の入力。
* *expr* -フィルター処理する式。
* *leftRange* -左の範囲の式 (包括)。
* *rightRange* -右側の範囲の式。

## <a name="returns"></a>戻り値

の述語*T* (*expr*  >=  *leftRange* and *expr*  <=  *rightRange*) がに評価される T 内の行 `true` 。

## <a name="examples"></a>例  

**' Between ' 演算子を使用した数値のフィルター処理**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 100 step 1
| where x between (50 .. 55)
```

|x|
|---|
|50|
|51|
|52|
|53|
|54|
|55|

**' Between ' 演算子を使用した datetime をフィルター処理しています**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|Count|
|---|
|476|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count 
```

|Count|
|---|
|476|
