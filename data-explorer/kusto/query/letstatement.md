---
title: Let ステートメント - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでステートメントを使用する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 890a6e21400048031e4ebd3df9749b6c47803e71
ms.sourcegitcommit: 653bfb3edf32553c52ef36b339c8b80713a601b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81524448"
---
# <a name="let-statement"></a>let ステートメント

Let ステートメントは、名前を式にバインドします。 let ステートメントが出現するスコープの残りの部分 (グローバル スコープまたは関数本体スコープ内) では、その名前を使用して、バインドされた値を参照できます。 その名前が以前に別の値にバインドされていた場合は、"最も内側の" let ステートメント バインドが使用されます。

ステートメントは、潜在的に複雑な式を複数の部分に分割し、それぞれが let ステートメントを通じて名前にバインドし、それらを一緒に構成できるように、モジュール性と再利用を改善します。 また、ユーザー定義の関数やビュー (結果が新しいテーブルのように見えるテーブルに対する式) を作成することもできます。

let ステートメントでバインドされる名前は、有効なエンティティ名である必要があります。

let ステートメントによってバインドされる式は、次のようになります。
* スカラータイプの
* 表形式の
* ユーザー定義関数 (ラムダ)

**構文**

`let`*名前*`=`*スカラー式* | *表形式式* | *関数定義式*

* *名前*: バインドする名前。 名前は有効なエンティティ名でなければならず、エンティティ名のエスケープ (例: `["Name with spaces"]`) が許可されます。 
* *スカラー式*: スカラー結果を持つ式で、その値が名前にバインドされます。 (例: `let one=1;`)。
* *表形式式*: 名前に値がバインドされる表形式の結果を持つ式。 (例: `Logs | where Timestamp > ago(1h)`)。
* *関数定義式*: 名前にバインドされるラムダ (匿名関数宣言) を生成する式。
  (例: `let f=(a:int, b:string) { strcat(b, ":", a) }`)。

ラムダ式の構文は次のとおりです。

[`view` `(`] [*表形式の引数*`,`] [ ] ス*カラー引数*]`)` `{` *関数本体*`}`

`TabularArguments`- [*表形式の引数 [* `:` `(`*AtrName* `:` *AtrType*] [ .`,``)`] [`,` ... ][`,`]

 または: - [*表形式の引数 ]* `:` `(` `*` `)`

`ScalarArguments`- [*ArgName* `:` *ArgType*] [ .`,`

* `view`パラメーターなしのラムダ (引数がないラムダ) にのみ表示され、"all tables" がクエリの場合 (たとえば、 を使用`union *`する場合) にバインド名が含まれることを示します。
* *表形式引数*は、表形式の式の正式な引数のリストです。
  各引数には、次の情報が含まれます。
  * *表形式*引数の名前を指定します。 その後、名前は*FunctionBody*に表示され、ラムダが呼び出されたときに特定の値にバインドされます。 
  * テーブル スキーマ定義 - 型を持つ属性のリスト (AtrName : AtrType)。
  ラムダ呼び出しで使用される表形式式には、一致する型を持つこれらの属性がすべて含まれている必要がありますが、これらに限定されません。 
  表形式式として '(*)' を使用できます。 この場合、ラムダ呼び出しでは任意の表形式式を使用でき、ラムダ式ではその列にアクセスできません。
  すべての表形式の引数は、スカラー引数の前に指定する必要があります。
* *スカラー引数*は、正式なスカラー引数のリストです。 
  各引数には、次の情報が含まれます。
  * *ArgName* - 正式なスカラー引数の名前。 その後、名前は*FunctionBody*に表示され、ラムダが呼び出されたときに特定の値にバインドされます。  
  * *ArgType* : 正式なスカラー引数の型を指定します。 現在、ラムダ引数の型としてサポートされている型は、 `bool` `string`、 `long` `datetime`、 `timespan` `real`、 `dynamic` 、 、 、 、 (これらの型の別名) です。

**複数のネストされた let ステートメント**

次の例に示すように、`;`複数の let ステートメントを区切り記号と共に使用できます。
最後のステートメントは、有効なクエリ式である必要があります。 

```kusto
let start = ago(5h); 
let period = 2h; 
T | where Time > start and Time < start + period | ...
```

入れ子になった let ステートメントは許可され、ラムダ式内で使用できます。
Let ステートメントと引数は、関数本体の現在および内部のスコープで表示されます。

```kusto
let start_time = ago(5h); 
let end_time = start_time + 2h; 
T | where Time > start_time and Time < end_time | ...
```

**使用例**

### <a name="using-let-to-define-constants"></a>let を使用して定数を定義する

次の例では、名前`x`をスカラー リテラル`1`にバインドし、表形式の式ステートメントで使用します。

```kusto
let x = 1;
range y from x to x step x
```

同じ例ですが、この場合は、let ステートメントの名前は次の`['name']`概念を使用して指定されます。

```kusto
let ['x'] = 1;
range y from x to x step x
```

スカラー値に let を使用するもう 1 つの例を示します。

```kusto
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

### <a name="using-multiple-let-statements"></a>複数の let ステートメントを使用する

次の例では、あるステートメント (`foo2`) が別の`foo1`( ) を使用する 2 つの let ステートメントを定義しています。

```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="using-materialize-function"></a>マテリアライズ機能の使用

[`materialize`](materializefunction.md)関数は、クエリの実行中にサブクエリの結果をキャッシュできます。 

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
