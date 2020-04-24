---
title: オペレーターとして - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでオペレーターとして説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 05dc96fb7eec773d1e55d8b94a33cdda928622ff
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518430"
---
# <a name="as-operator"></a>as 演算子

名前を演算子の入力テーブル式にバインドするため、クエリは、クエリを壊さずにテーブル式の値を複数回参照し、 [let ステートメント](letstatement.md)を使用して名前をバインドできます。

**構文**

*T* `|` T `as` `hint.materialized` [ `=` ]*名前*`true`

**引数**

* *T*: 表形式の式。
* *名前*: 表形式の式の一時的な名前。
* `hint.materialized`:`true`に設定すると、表形式の式の値は[、materialize()](./materializefunction.md)関数呼び出しでラップされたかのように具体化されます。

**メモ**

* 指定された`as`名前は[、union](./unionoperator.md)`source_`の`withsource=`列 、 [find](./findoperator.md)の列、 および`$table`[検索](./searchoperator.md)の列で使用されます。

* [結合](./joinoperator.md)の外部の表形式入力 (`$left`) で演算子を使用して名前を付けた表形式の式は、結合の表`$right`形式の内部入力 ( ) でも使用できます。

**使用例**

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