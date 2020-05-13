---
title: Let ステートメント-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの Let ステートメントについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c2e21f0b41f34b469e409109a2586f3e5fd98fa5
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271470"
---
# <a name="let-statement"></a>let ステートメント

ステートメントを使用して、式に名前をバインドします。 Let ステートメントが出現する残りのスコープ (グローバルスコープまたは関数本体のスコープ) では、その名前を使用してバインドされた値を参照できます。 その名前が既に別の値にバインドされている場合は、"最も内側の" let ステートメントのバインドが使用されます。

ステートメントでは、可能性がある複雑な式を複数の部分に分割し、それぞれ let ステートメントを使用して名前にバインドし、それらをまとめて構成できるため、モジュール化と再利用が向上します。 また、ユーザー定義関数およびビュー (結果が新しいテーブルのように見えるテーブルに対する式) を作成するために使用することもできます。

Let ステートメントでバインドされる名前は、有効なエンティティ名である必要があります。

Let ステートメントでバインドされた式は次のようになります。
* スカラー型の
* 表形式型の
* ユーザー定義関数 (ラムダ)

**構文**

`let`*名前* `=`*ScalarExpression*  | *TabularExpression*  | *Functiondefinitionexpression*

* *名前*: バインドする名前。 名前は有効なエンティティ名にする必要があり、エンティティ名のエスケープ (例: など `["Name with spaces"]` ) を使用できます。 
* *ScalarExpression*: 値が名前にバインドされるスカラー結果を含む式。 (例: `let one=1;`)。
* *TabularExpression*: 値が名前にバインドされる表形式の結果を含む式。 (例: `Logs | where Timestamp > ago(1h)`)。
* *Functiondefinitionexpression*: 名前にバインドされるラムダ (匿名関数宣言) を生成する式。
  (例: `let f=(a:int, b:string) { strcat(b, ":", a) }`)。

ラムダ式の構文は次のとおりです。

[ `view` ] `(` [*TabularArguments*] [ `,` ] [*ScalarArguments*] `)` `{` *functionbody*`}`

`TabularArguments`-[*TabularArgName* `:` `(` [*atrname* `:` *atrname*] [ `,` ...] `)` ][`,` ... ][`,`]

 または:-[*TabularArgName* `:` `(` `*` `)` ]

`ScalarArguments`-[*Argname* `:` *argname*] [ `,` ...]

* `view`は、パラメーターなしのラムダ (引数を含まない) にのみ表示され、"すべてのテーブル" がクエリである場合 (たとえば、を使用している場合) に、バインドされた名前が含まれることを示し `union *` ます。
* *TabularArguments*は、正式な表形式の式引数の一覧です。
  各引数には次のものがあります。
  * *TabularArgName* -正式な表形式引数の名前。 名前は*Functionbody*に表示され、ラムダが呼び出されたときに特定の値にバインドされます。 
  * テーブルスキーマ定義-型 (AtrName: Atrname) を持つ属性の一覧。
  ラムダ呼び出しで使用される表形式の式は、これらのすべての属性に一致する型を持つ必要がありますが、これらに限定されません。 
  ' (*) ' は、表形式の式として使用できます。 この場合、任意の表形式の式をラムダ呼び出しで使用できます。また、ラムダ式では、そのいずれの列にもアクセスできません。
  すべての表形式引数は、スカラー引数の前に記述する必要があります。
* *ScalarArguments*は、正式なスカラー引数の一覧です。 
  各引数には次のものがあります。
  * *Argname* -正式なスカラー引数の名前。 名前は*Functionbody*に表示され、ラムダが呼び出されたときに特定の値にバインドされます。  
  * *Argtype* -正式なスカラー引数の型。 現在、ラムダ引数の型としてサポートされている型は、、、、、、、およびです。また、これらの型のエイリアスについても同様 `bool` `string` `long` `datetime` `timespan` `real` `dynamic` です。

**複数の入れ子になった let ステートメント**

次の例に示すように、複数の let ステートメントを区切り記号と共に使用でき `;` ます。
最後のステートメントは、有効なクエリ式である必要があります。 

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

**使用例**

### <a name="using-let-to-define-constants"></a>Let を使用して定数を定義する

次の例では、名前を `x` スカラーリテラルにバインド `1` し、テーブル式ステートメントで使用します。

```kusto
let x = 1;
range y from x to x step x
```

同じ例ですが、この場合は let ステートメントの名前が概念を使用して与えられ `['name']` ます。

```kusto
let ['x'] = 1;
range y from x to x step x
```

ただし、スカラー値に let を使用するもう1つの例を次に示します。

```kusto
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

### <a name="using-multiple-let-statements"></a>複数の let ステートメントの使用

次の例では、1つのステートメント ( `foo2` ) が別の () を使用する2つの let ステートメントを定義して `foo1` います。

```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="using-materialize-function"></a>具体化関数の使用

[`materialize`](materializefunction.md)関数を使用すると、クエリの実行時にサブクエリの結果をキャッシュできます。 

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

|Day1|Day2|割合|
|---|---|---|
|2016-05-01 00:00: 00.0000000|2016-05-02 00:00: 00.0000000|34.0645725975255|
|2016-05-01 00:00: 00.0000000|2016-05-03 00:00: 00.0000000|16.618368960101|
|2016-05-02 00:00: 00.0000000|2016-05-03 00:00: 00.0000000|14.6291376489636|
