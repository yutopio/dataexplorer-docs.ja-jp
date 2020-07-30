---
title: in と notin 演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの in 演算子と notin 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: ab2132908dad26f5f21cf945a1af4af1b8a049cd
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347393"
---
# <a name="in-and-in-operators"></a>in および !in 演算子

指定された値のセットに基づいてレコードセットをフィルター処理します。

```kusto
Table1 | where col in ('value1', 'value2')
```

## <a name="syntax"></a>構文

*大文字と小文字を区別する構文:*

*T* `|` `where` *col* `in` `(` *スカラー式の*T col リスト`)`   
*T* `|` `where` *列* `in` `(` *表形式式*`)`   
 
*T* `|` `where` *col* `!in` `(` *スカラー式の*T col リスト`)`  
*T* `|` `where` *列* `!in` `(` *表形式式*`)`   

*大文字と小文字を区別しない構文:*

*T* `|` `where` *col* `in~` `(` *スカラー式の*T col リスト`)`   
*T* `|` `where` *列* `in~` `(` *表形式式*`)`   
 
*T* `|` `where` *col* `!in~` `(` *スカラー式の*T col リスト`)`  
*T* `|` `where` *列* `!in~` `(` *表形式式*`)`   

## <a name="arguments"></a>引数

* *T* -レコードをフィルター処理するための表形式の入力。
* *col* -フィルター処理する列。
* *式の一覧*-表形式、スカラー式、またはリテラル式のコンマ区切りのリスト。
* *表形式式*-値のセットを含む表形式の式です。 式に複数の列がある場合は、最初の列が使用されます。

## <a name="returns"></a>戻り値

述語がである*T*内の行 `true` 。

**ノート**

* 式リストでは、最大値を生成でき `1,000,000` ます。
* 入れ子になった配列は、1つの値リストにフラット化されます。 たとえば、`x in (dynamic([1,[2,3]]))` を `x in (1,2,3)` にします。
* テーブル式では、結果セットの最初の列が選択されます。
* 演算子に ' ~ ' を追加すると、値の検索で大文字と小文字が区別さ `x in~ (expression)` `x !in~ (expression)` れません: または。

**例:**  

**' In ' 演算子の単純な使用法:**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Count|
|---|
|4775|  


**' In ~ ' 演算子の単純な使用法:**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in~ ("Florida", "Georgia", "New York") 
| count
```

|Count|
|---|
|4775|  

**'! In ' 演算子の単純な使用法は次のとおりです。**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State !in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Count|
|---|
|54291|  


**動的配列の使用:**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

同じクエリは次のように記述できます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

**Top とその他の例:**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

**関数によって返される静的リストを使用する場合:**  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | where State in (InterestingStates()) | count

```

|Count|
|---|
|4775|  

関数の定義。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.show function InterestingStates
```

|名前|パラメーター|本文|Folder|DocString|
|---|---|---|---|---|
|InterestingStates|()|{dynamic (["ワシントン", "フロリダ", "ジョージア", "ニューヨーク"])}
