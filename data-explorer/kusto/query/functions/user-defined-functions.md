---
title: ユーザー定義関数 - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer のユーザー定義関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.localizationpriority: high
ms.openlocfilehash: 92627b3325a7a2ba8e2e4d58a82ebf6db3977221
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512878"
---
# <a name="user-defined-functions"></a>ユーザー定義関数

**ユーザー定義関数** は、クエリ自体の一部として定義できる (**アドホック関数**)、またはデータベース メタデータの一部として保持できる (**ストアド関数**) 再利用可能なサブクエリです。 ユーザー定義関数は、**名前** を使用して呼び出され、0 個以上の **入力引数** (スカラーまたは表形式) が指定され、関数の **本体** に基づいて 1 つの値 (スカラーまたは表形式) が生成されます。

ユーザー定義関数は、次の 2 つのカテゴリのいずれかに属します。

* スカラー関数 
* 表形式関数 

関数の入力引数と出力によってスカラーか表形式かが決まり、それによって使用方法も決まります。 

## <a name="scalar-function"></a>スカラー関数

* 入力引数が 0 個か、入力引数がすべてスカラー値です
* 1 つのスカラー値を生成します
* スカラー式が許容されている任意の場所に使用できます
* 定義されている行コンテキストのみを使用できます
* アクセス可能なスキーマ内のテーブル (およびビュー) のみを参照できます

## <a name="tabular-function"></a>表形式関数

* 1 つ以上の表形式入力引数、および 0 個以上のスカラー入力引数を受け取ります (かつ、または)
* 1 つの表形式の値を生成します

## <a name="function-names"></a>関数名

有効なユーザー定義関数名は、他のエンティティと同じ[識別子の名前付け規則](../schema-entities/entity-names.md#identifier-naming-rules)に従っている必要があります。

また、名前は定義の範囲内で一意である必要があります。

> [!NOTE]
> テーブルまたは関数名のクエリを実行したときに、ストアド関数とテーブルの両方が同じ名前の場合はストアド関数が優先されます。

## <a name="input-arguments"></a>入力引数

有効なユーザー定義関数の規則は次のとおりです。

* ユーザー定義関数には、0 個以上の厳密に型指定された入力引数のリストがあります。
* 入力引数には、名前、型、および (スカラー引数の場合) [既定値](#default-values)があります。
* 入力引数の名前は識別子です。
* 入力引数の型は、スカラー データ型または表形式スキーマのいずれかです。

入力引数リストは、構文的には、かっこで囲まれたコンマ区切りの引数定義のリストです。 各引数の定義は次のように指定します

```
ArgName:ArgType [= ArgDefaultValue]
```
 表形式引数の場合、*ArgType* はテーブル定義と同じ構文 (かっこと、列名と型のペアのリスト) であり、さらに "任意の表形式スキーマ" を示す 1 つの `(*)` がサポートされています。

次に例を示します。

|構文                        |入力引数リストの説明                                 |
|------------------------------|-----------------------------------------------------------------|
|`()`                          |引数なし|
|`(s:string)`                  |型 `string` の値を受け取る `s` という 1 つのスカラー引数|
|`(a:long, b:bool=true)`       |2 つのスカラー引数 (2 つ目には既定値があります)    |
|`(T1:(*), T2(r:real), b:bool)`|3 つの引数 (2 つの表形式引数と 1 つのスカラー引数)  |

> [!NOTE]
> 表形式入力引数とスカラー入力引数の両方を使用する場合は、すべての表形式入力引数をスカラー入力引数の前に配置します。

## <a name="examples"></a>例

スカラー関数:

```kusto
let Add7 = (arg0:long = 5) { arg0 + 7 };
range x from 1 to 10 step 1
| extend x_plus_7 = Add7(x), five_plus_seven = Add7()
```

引数を受け取らない表形式関数:

```kusto
let tenNumbers = () { range x from 1 to 10 step 1};
tenNumbers
| extend x_plus_7 = x + 7
```

表形式入力とスカラー入力の両方を受け取る表形式関数:

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v
};
MyFilter((range x from 1 to 10 step 1), 9)
```

|x|
|---|
|9|
|10|

列が指定されていない表形式入力を使用する表形式関数。
任意のテーブルを関数に渡すことができます。関数内でテーブル列を参照することはできません。

```kusto
let MyDistinct = (T:(*)) {
  T | distinct *
};
MyDistinct((range x from 1 to 3 step 1))
```

|x|
|---|
|1|
|2|
|3|

## <a name="declaring-user-defined-functions"></a>ユーザー定義関数の宣言

ユーザー定義関数の宣言では、以下を指定します。

* 関数の **名前**
* 関数の **スキーマ** (受け取るパラメーターがある場合)
* 関数の **本体**

> [!Note]
> 関数のオーバーロードはサポートされていません。 同じ名前で入力スキーマが異なる複数の関数を作成することはできません。

> [!TIP]
> ラムダ関数には名前がありません。[let ステートメント](../letstatement.md)を使用して 1 つの名前にバインドされます。 そのため、ユーザー定義のストアド関数と考えることができます。
> 例:2 つの引数 (`s` という名前の `string` と `i` という名前の `long`) を受け取るラムダ関数の宣言。 (数値に変換した後の) 1 つ目と 2 つ目の積が返されます。 ラムダは名前 `f` にバインドされます。

```kusto
let f=(s:string, i:long) {
    tolong(s) * i
};
```

関数の **本体** には次のものが含まれます。

* 関数の戻り値 (スカラーまたは表形式の値) を返す 1 つのみの式。
* スコープが関数本体のものである任意の数 (0 個以上) の [let ステートメント](../letstatement.md)。 指定する場合、let ステートメントは、関数の戻り値を定義する式の前に配置する必要があります。
* 関数に使用するクエリ パラメーターを宣言する任意の数 (0 個以上) の[クエリ パラメータ ステートメント](../queryparametersstatement.md)。 指定する場合、関数の戻り値を定義する式の前に配置する必要があります。

> [!NOTE]
> クエリの "最上位" でサポートされている他の種類の[クエリ ステートメント](../statements.md)は、関数本体内ではサポートされていません。

### <a name="examples-of-user-defined-functions"></a>ユーザー定義関数の例 

**let ステートメントを使用するユーザー定義関数**

次の例では、名前 `Test` を 3 つの let ステートメントを使用するユーザー定義関数 (ラムダ) にバインドします。 出力は `70` です。

```kusto
let Test1 = (id: int) {
  let Test2 = 10;
  let Test3 = 10 + Test2 + id;
  let Test4 = (arg: int) {
      let Test5 = 20;
      Test2 + Test3 + Test5 + arg
  };
  Test4(10)
};
range x from 1 to Test1(10) step 1
| count
```

**パラメーターの既定値を定義するユーザー定義関数**

次の例は、3 つの引数を受け取る関数を示しています。 後者の 2 つには既定値があり、呼び出しサイトに存在する必要はありません。

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
print f(12, c=7) // Returns "12-b.default-7"
```

## <a name="invoking-a-user-defined-function"></a>ユーザー定義関数の呼び出し

引数を受け取らないユーザー定義関数は、その名前、またはその名前とかっこ内が空の引数リストのいずれかを使用して呼び出すことができます。

次に例を示します。

```kusto
// Bind the identifier a to a user-defined function (lambda) that takes
// no arguments and returns a constant of type long:
let a=(){123};
// Invoke the function in two equivalent ways:
range x from 1 to 10 step 1
| extend y = x * a, z = x * a() 
```

```kusto
// Bind the identifier T to a user-defined function (lambda) that takes
// no arguments and returns a random two-by-two table:
let T=(){
  range x from 1 to 2 step 1
  | project x1 = rand(), x2 = rand()
};
// Invoke the function in two equivalent ways:
// (Note that the second invocation must be itself wrapped in
// an additional set of parentheses, as the union operator
// differentiates between "plain" names and expressions)
union T, (T())
```

1 つ以上のスカラー引数を受け取るユーザー定義関数は、テーブル名とかっこ内の具象引数リストを使用して呼び出すことができます。

```kusto
let f=(a:string, b:string) {
  strcat(a, " (la la la)", b)
};
print f("hello", "world")
```

1 つ以上のテーブル引数 (および任意の数のスカラー引数) を受け取るユーザー定義関数は、テーブル名とかっこ内の具象引数リストを使用して呼び出すことができます。

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v 
};
MyFilter((range x from 1 to 10 step 1), 9)
```

また、演算子 `invoke` を使用して、1 つ以上のテーブル引数を受け取り、テーブルを返すユーザー定義関数を呼び出すこともできます。 この関数は、関数の最初の具象テーブル引数が `invoke` 演算子のソースである場合に役立ちます。

```kusto
let append_to_column_a=(T:(a:string), what:string) {
    T | extend a=strcat(a, " ", what)
};
datatable (a:string) ["sad", "really", "sad"]
| invoke append_to_column_a(":-)")
```

## <a name="default-values"></a>既定の値

次の条件下で、関数の一部のパラメーターに既定値が設定される場合があります。

* 既定値は、スカラー パラメーターにのみ設定されます。
* 既定値は常にリテラル (定数) です。 任意の計算を指定することはできません。
* 既定値のないパラメーターは、常に既定値のあるパラメーターの前に配置されます。
* 呼び出し元は、既定値のないすべてのパラメーターの値を指定し、関数の宣言と同じ順序で配置する必要があります。
* 呼び出し元は、既定値のあるパラメーターの値を指定する必要はありませんが、指定することはできます。
* 呼び出し元は、パラメーターの順序と一致しない順序で引数を指定することができます。 その場合は、引数に名前を付ける必要があります。

次の例では、2 つの同一のレコードを持つテーブルを返します。 `f` の 1 つ目の呼び出しでは、引数は完全に "スクランブル化" されているため、それぞれに明示的に名前が付けられます。

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
union
  (print x=f(c=7, a=12)), // "12-b.default-7"
  (print x=f(12, c=7))    // "12-b.default-7"
```

|x|
|---|
|12-b.default-7|
|12-b.default-7|

## <a name="view-functions"></a>ビュー関数

引数を受け取らず、表形式の式を返すユーザー定義関数は、**ビュー** としてマークできます。 ユーザー定義関数をビューとしてマークするということは、ワイルドカードのテーブル名解決が行われるたびに、関数がテーブルのように動作することを意味します。
次の例では、2 つのユーザー定義関数 `T_view` と `T_notview` を示しています。`union` のワイルドカード参照によって 1 つ目の関数のみがどのように解決されるかを示しています。

```kusto
let T_view = view () { print x=1 };
let T_notview = () { print x=2 };
union T*
```

## <a name="restrictions"></a>制限

次の制限事項が適用されます。

* ユーザー定義関数を使用して、関数が呼び出される行コンテキストによって変わる呼び出し情報を [toscalar()](../toscalarfunction.md) に渡すことはできません。
* 表形式の式を返すユーザー定義関数を、行コンテキストによって変わる引数を使用して呼び出すことはできません。
* 1 つ以上の表形式入力を受け取る関数をリモート クラスター上で呼び出すことはできません。
* スカラー関数をリモート クラスター上で呼び出すことはできません。

行コンテキストによって変わる引数を使用してユーザー定義関数を呼び出すことができるのは、ユーザー定義関数がスカラー関数のみで構成され、`toscalar()` を使用しない場合のみです。

**制限の例 1**

```kusto
// Supported:
// f is a scalar function that doesn't reference any tabular expression
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { now() + hours*1h };
Table2 | where Column != 123 | project d = f(10)

// Supported:
// f is a scalar function that references the tabular expression Table1,
// but is invoked with no reference to the current row context f(10):
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { toscalar(Table1 | summarize min(xdate) - hours*1h) };
Table2 | where Column != 123 | project d = f(10)

// Not supported:
// f is a scalar function that references the tabular expression Table1,
// and is invoked with a reference to the current row context f(Column):
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { toscalar(Table1 | summarize min(xdate) - hours*1h) };
Table2 | where Column != 123 | project d = f(Column)
```

**制限の例 2**

```kusto
// Not supported:
// f is a tabular function that is invoked in a context
// that expects a scalar value.
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { range x from 1 to hours step 1 | summarize make_list(x) };
Table2 | where Column != 123 | project d = f(Column)
```

## <a name="features-that-are-currently-unsupported-by-user-defined-functions"></a>ユーザー定義関数で現在サポートされていない機能

完全を期すために、現在サポートされていないユーザー定義関数についてよくリクエストされている機能を次に示します。

1.  関数のオーバーロード:現在、関数をオーバーロードする (つまり、同じ名前で入力スキーマが異なる複数の関数を作成する) 方法はありません。

2.  既定値:関数に対するスカラー パラメーターの既定値は、スカラー リテラル (定数) である必要があります。 さらに、ストアド関数の既定値を型 `dynamic` にすることはできません。
