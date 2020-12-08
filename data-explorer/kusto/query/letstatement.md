---
title: let ステートメント - Azure Data Explorer
description: この記事では、Azure Data Explorer の let ステートメントについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/09/2020
ms.localizationpriority: high
ms.openlocfilehash: c102637adfa1fd0340d28a67b52354956b511ada
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513315"
---
# <a name="let-statement"></a>let ステートメント

let ステートメントは、式に名前をバインドするために使用します。 let ステートメントが使用されているスコープの残りの部分では、名前を使用してそのバインドされた値を参照できます。 let ステートメントは、グローバル スコープ内または関数本体スコープ内で使用できます。
その名前が既に別の値にバインドされている場合は、"最も内側" の let ステートメントのバインドが使用されます。

let ステートメントを使用すると、潜在的に複雑な式を複数の部分に分割できるため、モジュール化と再利用が向上します。
let ステートメントによって各部分が名前にバインドされ、それらをまとめたもので全体が構成されます。 また、それらを使用して、ユーザー定義の関数やビューを作成することもできます。 ビューはテーブルに対する式であり、結果は新しいテーブルのように見えます。

> [!NOTE]
> let ステートメントによってバインドされる名前は、有効なエンティティ名である必要があります。

let ステートメントでは次の式をバインドできます。
* スカラー型
* 表形式の型
* ユーザー定義関数 (ラムダ)

## <a name="syntax"></a>構文

`let` *Name* `=` *ScalarExpression* | *TabularExpression* | *FunctionDefinitionExpression*

|フィールド  |定義  |例  |
|---------|---------|---------|
|*名前*   | バインドする名前。 名前は有効なエンティティ名である必要があります。    |エンティティ名のエスケープ (`["Name with spaces"]` など) が許可されています。      |
|*ScalarExpression*     |  その値が名前にバインドされるスカラーの結果になる式。  | `let one=1;`  |
|*TabularExpression*    | その値が名前にバインドされる表形式の結果になる式。   | `Logs | where Timestamp > ago(1h)`    |
|*FunctionDefinitionExpression*   | ラムダを生成する式。名前にバインドされる匿名の関数宣言。   |  `let f=(a:int, b:string) { strcat(b, ":", a) }`  |


### <a name="lambda-expressions-syntax"></a>ラムダ式の構文

[`view`] `(`[*TabularArguments*][`,`][*ScalarArguments*]`)` `{` *FunctionBody* `}`

`TabularArguments` - [*TabularArgName* `:` `(`[*AtrName* `:` *AtrType*] [`,` ... ]`)`] [`,` ... ][`,`]

 または

 [*TabularArgName* `:` `(` `*` `)`]

`ScalarArguments` - [*ArgName* `:` *ArgType*] [`,` ... ]


|フィールド  |定義  |例  |
|---------|---------|---------|
| **view** | 引数を持たないパラメーターなしのラムダでのみ使用できます。 "すべてのテーブル" がクエリである場合に、バインドされた名前が含まれることを示します。 | たとえば、`union *` を使用する場合です。|
| ***TabularArguments** _ | 式の表形式の仮引数のリスト。 
| 各表形式引数には次のものが含まれます。||
|<ul><li> _TabularArgName*</li></ul> | 表形式の仮引数の名前。 名前は *FunctionBody* で使用でき、ラムダが呼び出されると特定の値にバインドされます。 ||
|<ul><li>テーブルのスキーマ定義 </li></ul> | それらの型である属性のリスト| AtrName :AtrType|
| ***ScalarArguments** _ | スカラーの仮引数のリスト。 
|各スカラー引数には次のものが含まれます。||
|<ul><li>_ArgName*</li></ul> | スカラーの仮引数の名前。 名前は *FunctionBody* で使用でき、ラムダが呼び出されると特定の値にバインドされます。  |
| <ul><li>*ArgType* </li></ul>| スカラーの仮引数の型。 | 現在、ラムダ引数の型としてサポートされている型は、`bool`、`string`、`long`、`datetime`、`timespan`、`real`、`dynamic` (およびこれらの型への別名) だけです。

> [!NOTE]
>ラムダ呼び出しで使用される表形式の式には、一致する型のすべての属性が含まれている必要があります (ただし、これらに限定されるわけではありません)。
>
>表形式の式として `(*)` を使用できます。 
>
> 任意の表形式の式をラムダ呼び出しで使用でき、そのいずれの列もラムダ式ではアクセスできません。 
>
> すべての表形式の引数は、スカラー引数の前に記述する必要があります。

## <a name="multiple-and-nested-let-statements"></a>複数の入れ子になった let ステートメント

次の例に示すように、セミコロン `;` を区切り記号として使用することで、複数の let ステートメントを使用できます。

> [!NOTE]
> 最後のステートメントは、有効なクエリ式である必要があります。 

```kusto
let start = ago(5h); 
let period = 2h; 
T | where Time > start and Time < start + period | ...
```

let ステートメントを入れ子にすることができ、ラムダ式の内部で使用できます。
let ステートメントと引数は、関数本体の現在のスコープと内部のスコープで参照できます。

```kusto
let start_time = ago(5h); 
let end_time = start_time + 2h; 
T | where Time > start_time and Time < end_time | ...
```

## <a name="examples"></a>例

### <a name="use-let-function-to-define-constants"></a>let 関数を使用して定数を定義する

次の例では、名前 `x` をスカラー リテラル `1` にバインドした後、表形式の式ステートメントでそれを使用します。

```kusto
let x = 1;
range y from x to x step x
```

この例は前のものと似ていますが、`['name']` の概念を使用して let ステートメントの名前のみを指定しています。

```kusto
let ['x'] = 1;
range y from x to x step x
```

### <a name="use-let-for-scalar-values"></a>スカラー値に let を使用する

```kusto
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

### <a name="use-let-statement-with-arguments-for-scalar-calculation"></a>スカラー計算の引数で let ステートメントを使用する

この例では、スカラー計算の引数で let ステートメントを使用します。 2 つの数値を乗算する関数 `MultiplyByN` をクエリで定義します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let MultiplyByN = (val:long, n:long) { val * n };
range x from 1 to 5 step 1 
| extend result = MultiplyByN(x, 5)
```

|x|結果|
|---|---|
|1|5|
|2|10|
|3|15|
|4|20|
|5|25|

次の例では、入力の先頭と末尾から `1` を削除します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let TrimOnes = (s:string) { trim("1", s) };
range x from 10 to 15 step 1 
| extend result = TrimOnes(tostring(x))
```

|x|結果|
|---|---|
|10|0|
|11||
|12|2|
|13|3|
|14|4|
|15|5|


### <a name="use-multiple-let-statements"></a>複数の let ステートメントを使用する

この例では、2 つの let ステートメントを定義し、一方のステートメント (`foo2`) で他方 (`foo1`) を使用します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="use-the-view-keyword-in-a-let-statement"></a>let ステートメントで `view` キーワードを使用する

この例では、`view` キーワードと共に let ステートメントを使用する方法を示します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Range10 = view () { range MyColumn from 1 to 10 step 1 };
let Range20 = view () { range MyColumn from 1 to 20 step 1 };
search MyColumn == 5
```

|$table|MyColumn|
|---|---|
|Range10|5|
|Range20|5|


### <a name="use-materialize-function"></a>materialize 関数を使用する

[`materialize`](materializefunction.md) 関数を使用すると、クエリの実行時に、サブクエリの結果をキャッシュすることができます。 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let totalPagesPerDay = PageViews
| summarize by Page, Day = startofday(Timestamp)
| summarize count() by Day;
let materializedScope = PageViews
| summarize by Page, Day = startofday(Timestamp);
let cachedResult = materialize(materializedScope);
cachedResult
| project Page, Day1 = Day
| join kind = inner
(
    cachedResult
    | project Page, Day2 = Day
)
on Page
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

|Day1|Day2|パーセント|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|34.0645725975255|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.618368960101|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.6291376489636|
