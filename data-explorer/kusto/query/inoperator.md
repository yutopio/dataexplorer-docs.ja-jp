---
title: in 演算子と notin 演算子 - Azure Data Explorer
description: この記事では、Azure Data Explorer のin 演算子と notin 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2019
ms.localizationpriority: high
ms.openlocfilehash: ffb24abe744bfbe3f7f95336edf0263becfa7ec9
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513252"
---
# <a name="in-and-in-operators"></a>in および !in 演算子

指定された値のセットに基づいてレコード セットをフィルター処理します。

```kusto
Table1 | where col in ('value1', 'value2')
```

> [!NOTE]
> * 演算子に '~' を追加すると、値の検索で大文字と小文字が区別されなくなります (`x in~ (expression)` または `x !in~ (expression)`)。
> * 表形式では、結果セットの最初の列が選択されます。
> * 式リストによって、最大 `1,000,000` 個の値を生成できます。
> * 入れ子になった配列は、単一の値リストにフラット化されます。 たとえば、`x in (dynamic([1,[2,3]]))` を `x in (1,2,3)` にします。
 
## <a name="syntax"></a>構文

### <a name="case-sensitive-syntax"></a>大文字と小文字が区別される構文

*T* `|` `where` *col* `in` `(`*list of scalar expressions*`)`   
*T* `|` `where` *col* `in` `(`*tabular expression*`)`   
 
*T* `|` `where` *col* `!in` `(`*list of scalar expressions*`)`  
*T* `|` `where` *col* `!in` `(`*tabular expression*`)`   

### <a name="case-insensitive-syntax"></a>大文字と小文字が区別されない構文

*T* `|` `where` *col* `in~` `(`*list of scalar expressions*`)`   
*T* `|` `where` *col* `in~` `(`*tabular expression*`)`   
 
*T* `|` `where` *col* `!in~` `(`*list of scalar expressions*`)`  
*T* `|` `where` *col* `!in~` `(`*tabular expression*`)`   

## <a name="arguments"></a>引数

* *T* - フィルター処理するレコードが含まれる表形式の入力。
* *col* - フィルター処理する列。
* *list of expressions* -表形式、スカラー式、またはリテラル式のコンマ区切りの一覧です。
* *tabular expression* - 値のセットを含む表形式の式です。 式に複数の列がある場合は、最初の列が使用されます。

## <a name="returns"></a>戻り値

述語が `true` である *T* 内の行。

## <a name="examples"></a>例  

### <a name="use-in-operator"></a>'in' 演算子を使用

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Count|
|---|
|4775|  

### <a name="use-in-operator"></a>'in~' 演算子を使用  

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State in~ ("Florida", "Georgia", "New York") 
| count
```

|Count|
|---|
|4775|  

### <a name="use-in-operator"></a>'!in' 演算子を使用

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State !in ("FLORIDA", "GEORGIA", "NEW YORK") 
| count
```

|Count|
|---|
|54291|  


### <a name="use-dynamic-array"></a>動的配列を使用

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

### <a name="subquery"></a>サブクエリ

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

同じクエリを次のように記述できます。

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

### <a name="top-with-other-example"></a>Top とその他の例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let Lightning_By_State = materialize(StormEvents | summarize lightning_events = countif(EventType == 'Lightning') by State);
let Top_5_States = Lightning_By_State | top 5 by lightning_events | project State; 
Lightning_By_State
| extend State = iif(State in (Top_5_States), State, "Other")
| summarize sum(lightning_events) by State 
```

| 州     | sum_lightning_events |
|-----------|----------------------|
| ALABAMA   | 29                   |
| WISCONSIN | 31                   |
| テキサス州     | 55                   |
| FLORIDA   | 85                   |
| GEORGIA   | 106                  |
| その他     | 415                  |

### <a name="use-a-static-list-returned-by-a-function"></a>関数によって返される静的リストを使用

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

|名前|パラメーター|本文|フォルダー|DocString|
|---|---|---|---|---|
|InterestingStates|()|{ dynamic(["WASHINGTON", "FLORIDA", "GEORGIA", "NEW YORK"]) }
