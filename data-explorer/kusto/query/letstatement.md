---
title: Let ステートメント-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの Let ステートメントについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/09/2020
ms.openlocfilehash: 879b858904ac9f024f70dfef6096141a9ff81bd7
ms.sourcegitcommit: b8415e01464ca2ac9cd9939dc47e4c97b86bd07a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88028478"
---
# <a name="let-statement"></a>let ステートメント

ステートメントを使用して、式に名前をバインドします。 Let ステートメントが出現するスコープの残りの部分では、その名前を使用してバインドされた値を参照できます。 Let ステートメントは、グローバルスコープ内、または関数本体のスコープ内にある場合があります。
その名前が既に別の値にバインドされている場合は、"最も内側の" let ステートメントのバインドが使用されます。

ステートメントを使用すると、潜在的に複雑な式を複数の部分に分割できるため、モジュール化と再利用が向上します。
各部分は let ステートメントを使用して名前にバインドされ、全体を構成します。 また、ユーザー定義関数およびビューを作成するために使用することもできます。 ビューは、新しいテーブルのように結果が表示されるテーブルに対する式です。

> [!NOTE]
> Let ステートメントでバインドされる名前は、有効なエンティティ名である必要があります。

Let ステートメントでバインドされた式は次のようになります。
* スカラー型
* 表形式の型
* ユーザー定義関数 (ラムダ)

## <a name="syntax"></a>Syntax

`let`*名前* `=`*ScalarExpression*  | *TabularExpression*  | *Functiondefinitionexpression*

|フィールド  |定義  |例  |
|---------|---------|---------|
|*名前*   | バインドする名前。 名前は有効なエンティティ名である必要があります。    |などのエンティティ名のエスケープ `["Name with spaces"]` は許可されています。      |
|*ScalarExpression*     |  値が名前にバインドされたスカラー結果を含む式。  | `let one=1;`  |
|*TabularExpression*    | 値が名前にバインドされた表形式の結果を含む式。   | `Logs | where Timestamp > ago(1h)`    |
|*FunctionDefinitionExpression*   | ラムダ (名前にバインドされる匿名関数宣言) を生成する式。   |  `let f=(a:int, b:string) { strcat(b, ":", a) }`  |


### <a name="lambda-expressions-syntax"></a>ラムダ式の構文

[ `view` ] `(` [*TabularArguments*] [ `,` ] [*ScalarArguments*] `)` `{` *functionbody*`}`

`TabularArguments`-[*TabularArgName* `:` `(` [*atrname* `:` *atrname*] [ `,` ...] `)` ][`,` ... ][`,`]

 または

 [*TabularArgName* `:` `(``*` `)`]

`ScalarArguments`-[*Argname* `:` *argname*] [ `,` ...]


|フィールド  |定義  |例  |
|---------|---------|---------|
| **view** | は、引数を持たないパラメーターなしのラムダにのみ出現する可能性があります。 これは、"すべてのテーブル" がクエリである場合に、バインドされた名前が含まれることを示します。 | たとえば、を使用する場合 `union *` です。|
| ***TabularArguments*** | 正式な表形式の式の引数の一覧。 
| 各表形式引数には次のものがあります。||
|<ul><li> *TabularArgName*</li></ul> | 正式な表形式引数の名前。 名前は*Functionbody*に出現し、ラムダが呼び出されたときに特定の値にバインドされることがあります。 ||
|<ul><li>テーブルスキーマ定義 </li></ul> | 属性のリストとその型| AtrName: Atrname|
| ***ScalarArguments*** | 正式なスカラー引数の一覧。 
|各スカラー引数には次のものがあります。||
|<ul><li>*ArgName*</li></ul> | 正式なスカラー引数の名前。 名前は*Functionbody*に出現し、ラムダが呼び出されたときに特定の値にバインドされることがあります。  |
| <ul><li>*ArgType* </li></ul>| 仮引数の型。 | 現在、ラムダ引数の型としてサポートされている型は、、、、、、、およびです。また、これらの型のエイリアスについても同様 `bool` `string` `long` `datetime` `timespan` `real` `dynamic` です。

> [!NOTE]
>ラムダ呼び出しで使用される表形式の式には、一致する型のすべての属性が含まれている必要があります (ただし、これらに限定されるわけではありません)。
>
>`(*)`表形式の式として使用できます。 
>
> 任意の表形式の式は、ラムダ呼び出しで使用でき、そのいずれの列もラムダ式ではアクセスできません。 
>
> すべての表形式引数は、スカラー引数の前に記述する必要があります。

## <a name="multiple-and-nested-let-statements"></a>複数の入れ子になった let ステートメント

次の例に示すように、複数の let ステートメントをセミコロン、、区切り記号と共に使用でき `;` ます。

> [!NOTE]
> 最後のステートメントは、有効なクエリ式である必要があります。 

```kusto
let start = ago(5h); 
let period = 2h; 
T | where Time > start and Time < start + period | ...
```

入れ子になった let ステートメントは許可されており、ラムダ式の内部で使用できます。
ステートメントと引数は、関数本体の現在のスコープと内部スコープで参照できます。

```kusto
let start_time = ago(5h); 
let end_time = start_time + 2h; 
T | where Time > start_time and Time < end_time | ...
```

## <a name="examples"></a>例

### <a name="use-let-function-to-define-constants"></a>Let 関数を使用して定数を定義する

次の例では、名前を `x` スカラーリテラルにバインド `1` し、テーブル式ステートメントで使用します。

```kusto
let x = 1;
range y from x to x step x
```

この例は前の例と似ていますが、let ステートメントの名前だけが概念を使用して指定されてい `['name']` ます。

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

### <a name="use-let-statement-with-arguments-for-scalar-calculation"></a>スカラー計算の引数と共に let ステートメントを使用する

この例では、スカラー計算の引数と共に let ステートメントを使用します。 このクエリでは、 `MultiplyByN` 2 つの数値を乗算する関数を定義します。

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

次の例では、入力から先頭/末尾の 1 ( `1` ) を削除します。

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

この例では、1つのステートメント ( `foo2` ) が別の () を使用する2つの let ステートメント `foo1` を定義します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="use-the-view-keyword-in-a-let-statement"></a>`view`Let ステートメントでキーワードを使用する

この例では、let ステートメントをキーワードと共に使用する方法を示し `view` ます。

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


### <a name="use-materialize-function"></a>具体化関数を使用する

[`materialize`](materializefunction.md)関数を使用すると、クエリの実行時にサブクエリの結果をキャッシュすることができます。 

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
|2016-05-01 00:00: 00.0000000|2016-05-02 00:00: 00.0000000|34.0645725975255|
|2016-05-01 00:00: 00.0000000|2016-05-03 00:00: 00.0000000|16.618368960101|
|2016-05-02 00:00: 00.0000000|2016-05-03 00:00: 00.0000000|14.6291376489636|
