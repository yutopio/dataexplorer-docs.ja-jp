---
title: in および notin 演算子 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの演算子と notin 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: bd247de2bd211ae7be3da449e940899d2e8bb475
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513806"
---
# <a name="in-and-in-operators"></a>in および !in 演算子

指定された値のセットに基づいてレコードセットをフィルタ処理します。

```kusto
Table1 | where col in ('value1', 'value2')
```

**構文**

*大文字と小文字を区別する構文:*

*T*`|``where`*スカラー式の T* *col*`in``(`リスト`)`   
*T* `|` T `where` *col* `in` col `(`*表形式の式*`)`   
 
*T*`|``where`*スカラー式の T* *col*`!in``(`リスト`)`  
*T* `|` T `where` *col* `!in` col `(`*表形式の式*`)`   

*大文字と小文字を区別しない構文:*

*T*`|``where`*スカラー式の T* *col*`in~``(`リスト`)`   
*T* `|` T `where` *col* `in~` col `(`*表形式の式*`)`   
 
*T*`|``where`*スカラー式の T* *col*`!in~``(`リスト`)`  
*T* `|` T `where` *col* `!in~` col `(`*表形式の式*`)`   

**引数**

* *T* - レコードをフィルター処理する表形式の入力。
* *col* - フィルターする列。
* *式のリスト*- 表形式、スカラー式、またはリテラル式のコンマ区切りリスト  
* *表形式の式*- 値のセットを持つ表形式の式 (ケース式に複数の列がある場合は、最初の列が使用されます)

**戻り値**

述部が対象となる*T*の行`true`

**メモ**

* 式リストは最大の値を`1,000,000`生成できます    
* ネストされた配列は、値`x in (dynamic([1,[2,3]]))`の単一のリストに平坦化されます。`x in (1,2,3)` 
* 表形式の式の場合、結果セットの最初の列が選択されます。   
* 演算子に '~' を追加すると、値の大文字`x in~ (expression)`と`x !in~ (expression)`小文字が区別されません。

**例:**  

**'in' 演算子の単純な使用方法:**  

```kusto
StormEvents 
| where State in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Count|
|---|
|4775|  


**'in~' 演算子の単純な使用方法:**  

```kusto
StormEvents 
| where State in~ ("Florida", "Georgia", "New York") 
| count
```

|Count|
|---|
|4775|  

**'!in' 演算子の簡単な使用方法:**  

```kusto
StormEvents 
| where State !in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Count|
|---|
|54291|  


**動的配列の使用:**
```kusto
let states = dynamic(['FLORIDA', 'ATLANTIC SOUTH', 'GEORGIA']);
StormEvents 
| where State in (states)
| count
```

|Count|
|---|
|3218|


**サブクエリの例:**  

```kusto
// Using subquery
let Top_5_States = 
StormEvents
| summarize count() by State
| top 5 by count_; 
StormEvents 
| where State in (Top_5_States) 
| count
```

同じクエリを次のように記述できます。

```kusto
// Inline subquery 
StormEvents 
| where State in (
    ( StormEvents
    | summarize count() by State
    | top 5 by count_ )
) 
| count
```

|Count|
|---|
|14242|  

**他の例とトップ:**  

```kusto
let Lightning_By_State = materialize(StormEvents | summarize lightning_events = countif(EventType == 'Lightning') by State);
let Top_5_States = Lightning_By_State | top 5 by lightning_events | project State; 
Lightning_By_State
| extend State = iif(State in (Top_5_States), State, "Other")
| summarize sum(lightning_events) by State 
```

| State     | sum_lightning_events |
|-----------|----------------------|
| ALABAMA   | 29                   |
| ウィスコンシン | 31                   |
| テキサス州     | 55                   |
| フロリダ   | 85                   |
| グルジア   | 106                  |
| その他     | 415                  |

**関数から返される静的リストを使用する場合:**  

```kusto
StormEvents | where State in (InterestingStates()) | count

```

|Count|
|---|
|4775|  


関数定義は次のとおりです。  

```kusto
.show function InterestingStates
```

|名前|パラメーター|Body|Folder|ドクスト文字列|
|---|---|---|---|---|
|興味深い状態|()|{ダイナミック(「ワシントン」、「フロリダ」、「ジョージア」、「ニューヨーク」))
