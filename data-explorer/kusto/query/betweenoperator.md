---
title: オペレーター間 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのオペレーター間について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 336d24edbcd7574f0c4b6375b4b09014b38d10ec
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517852"
---
# <a name="between-operator"></a>between 演算子

両端を含む範囲内に入っている値が一致として出力されます。

```kusto
Table1 | where Num1 between (1 .. 10)
Table1 | where Time between (datetime(2017-01-01) .. datetime(2017-01-01))
```

`between`は、任意の数値式、日時式、またはタイムスパン式に対して操作できます。
 
**構文**

*T* `|` T `where` *expr*`between`*leftRange*` .. `*rightRange*左範囲右範囲`(``)`   
 
*expr*式が日時の場合は、別の構文的な構文の構文が提供されます。

*T*`|`左`where`*expr*`between`から`(`*leftRangeDateTime*離` .. `れ、*右の範囲時間*`)`   

**引数**

* *T* - レコードが一致する表形式の入力。
* *expr* - フィルター処理する式を指定します。
* *左の範囲*- 左の範囲の式(含む)。
* *右の範囲*- 右の範囲の式 (含む)。

**戻り値**

*T*の行の行 (*expr* >= leftRange および*expr* <= *rightRange* ) の述語が に評価されます。*rightRange* `true`

**使用例**  

**'between' 演算子を使用して数値をフィルター処理する**  

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

**'between' 演算子を使用した日時のフィルタリング**  


```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count 
```

|Count|
|---|
|476|


```kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count 
```

|Count|
|---|
|476|