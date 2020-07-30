---
title: as 演算子-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの as 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f9d7a60b3c39fb0b7357c2bbe68533252f794347
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349484"
---
# <a name="as-operator"></a>as 演算子

演算子の入力表形式式に名前をバインドします。これにより、クエリを中断せずに、 [let ステートメント](letstatement.md)を使用して名前をバインドすることなく、テーブル式の値を複数回参照できます。

## <a name="syntax"></a>構文

*T* `|` `as` [ `hint.materialized` `=` `true` ]*名前*

## <a name="arguments"></a>引数

* *T*: 表形式の式。
* *名前*: 表形式の式の一時的な名前。
* `hint.materialized`: に設定されている場合 `true` 、テーブル式の値は、[具体化 ()](./materializefunction.md)関数呼び出しによってラップされているかのように具体化されます。

**ノート**

* によって指定された名前は、 `as` `withsource=` [union](./unionoperator.md)の列、 `source_` [find](./findoperator.md)列、および `$table` [search](./searchoperator.md)の列で使用されます。

* [結合](./joinoperator.md)の外側の表形式入力 () で演算子を使用して名前が付けられた表形式の式は、 `$left` 結合の表形式の内部入力 () でも使用でき `$right` ます。

## <a name="examples"></a>使用例

```kusto
// 1. In the following 2 example the union's generated TableName column will consist of 'T1' and 'T2'
range x from 1 to 10 step 1 
| as T1 
| union withsource=TableName T2

union withsource=TableName (range x from 1 to 10 step 1 | as T1), T2

// 2. In the following example, the 'left side' of the join will be: 
//      MyLogTable filtered by type == "Event" and Name == "Start"
//    and the 'right side' of the join will be: 
//      MyLogTable filtered by type == "Event" and Name == "Stop"
MyLogTable  
| where type == "Event"
| as T
| where Name == "Start"
| join (
    T
    | where Name == "Stop"
) on ActivityId
```