---
title: ユーザー定義関数-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのユーザー定義関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 769ebc16da0780f1d1832dcbf49bad516c47abd3
ms.sourcegitcommit: 2764e739b4ad51398f4f0d3a9742d7168c4f5fd7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "91712019"
---
# <a name="user-defined-functions"></a>ユーザー定義関数

**ユーザー定義関数** は、クエリ自体 (**アドホック関数**) の一部として定義したり、データベースメタデータの一部として永続化したりすることができる再利用可能なサブクエリです (**ストアド関数**)。 ユーザー定義関数は、 **名前**を使用して呼び出されます。これには、0個以上の **入力引数** (スカラーまたは表形式) が用意されており、関数 **本体**に基づいて単一の値 (スカラーまたは表形式) が生成されます。

ユーザー定義関数は、次の2つのカテゴリのいずれかに属します。

* スカラー関数 
* 表形式関数 

関数の入力引数と出力によって、それがスカラーと表形式のどちらであるかが判断され、それによってどのように使用されるかが決まります。 

## <a name="scalar-function"></a>スカラー関数

* の入力引数が0であるか、またはすべての入力引数がスカラー値です
* 1つのスカラー値を生成します
* スカラー式が許可される場所であれば使用できます
* 定義されている行コンテキストのみを使用できます
* アクセス可能なスキーマ内のテーブル (およびビュー) のみを参照できます

## <a name="tabular-function"></a>表形式関数

* 1つ以上の表形式の入力引数、および0個以上のスカラー入力引数を受け取ります。
* 1つの表形式の値を生成します

## <a name="function-names"></a>関数名

有効なユーザー定義関数名は、他のエンティティと同じ [識別子の名前付け規則](../schema-entities/entity-names.md#identifier-naming-rules) に従う必要があります。

名前は、定義のスコープ内でも一意である必要があります。

## <a name="input-arguments"></a>入力引数

有効なユーザー定義関数は、次の規則に従います。

* ユーザー定義関数には、0個以上の入力引数の厳密に型指定されたリストがあります。
* 入力引数には、名前、型、および (スカラー引数の場合) [既定値](#default-values)があります。
* 入力引数の名前は識別子です。
* 入力引数の型は、スカラーデータ型または表形式スキーマのいずれかになります。

構文的には、入力引数リストは、かっこで囲まれた引数定義のコンマ区切りのリストです。 各引数の定義はとして指定されます。

```
ArgName:ArgType [= ArgDefaultValue]
```
 テーブル引数の場合、 *Argtype* にはテーブル定義と同じ構文 (かっこと、列名と型のペアのリスト) があり、 `(*)` "任意の表形式スキーマ" を指定するための追加のサポートがあります。

次に例を示します。

|Syntax                        |入力引数リストの説明                                 |
|------------------------------|-----------------------------------------------------------------|
|`()`                          |引数なし|
|`(s:string)`                  |`s`型の値を取得する単一のスカラー引数が呼び出されました`string`|
|`(a:long, b:bool=true)`       |2つのスカラー引数 (2 番目の引数に既定値がある)    |
|`(T1:(*), T2(r:real), b:bool)`|3つの引数 (2 つのテーブル引数と1つのスカラー引数)  |

> [!NOTE]
> 表形式の入力引数とスカラー入力引数の両方を使用する場合は、すべての表形式の入力引数をスカラー入力引数の前に配置します。

## <a name="examples"></a>例

スカラー関数:

```kusto
let Add7 = (arg0:long = 5) { arg0 + 7 };
range x from 1 to 10 step 1
| extend x_plus_7 = Add7(x), five_plus_seven = Add7()
```

引数を取らない表形式関数:

```kusto
let tenNumbers = () { range x from 1 to 10 step 1};
tenNumbers
| extend x_plus_7 = x + 7
```

表形式の入力とスカラー入力の両方を取得する表形式関数:

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

列が指定されていない表形式の入力を使用する表形式関数。
任意のテーブルを関数に渡すことができ、関数内でテーブル列を参照することはできません。

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

## <a name="declaring-user-defined-functions"></a>宣言 (ユーザー定義関数を)

ユーザー定義関数の宣言では、次のことを行います。

* 関数 **名**
* 関数 **スキーマ** (パラメーターがあれば、それを受け取るパラメーター)
* 関数 **本体**

> [!Note]
> オーバーロード関数はサポートされていません。 同じ名前および異なる入力スキーマを使用して、複数の関数を作成することはできません。

> [!TIP]
> ラムダ関数には名前がなく、 [let ステートメント](../letstatement.md)を使用して名前にバインドされています。 そのため、ユーザー定義の格納関数と見なすことができます。
> 例: 2 つの引数 (と呼ばれる) を受け取るラムダ関数の宣言 `string` `s` `long` `i` 。 最初のの積 (数値に変換した後) と2番目のの積を返します。 ラムダは次の名前にバインドされ `f` ます。

```kusto
let f=(s:string, i:long) {
    tolong(s) * i
};
```

関数 **本体** には次のものが含まれます。

* 厳密に1つの式。関数の戻り値 (スカラー値または表形式の値) を提供します。
* [Let ステートメント](../letstatement.md)の任意の数 (0 個以上)。スコープは関数本体のものです。 指定した場合、let ステートメントは、関数の戻り値を定義する式の前に記述する必要があります。
* [クエリパラメーターステートメント](../queryparametersstatement.md)の任意の数 (0 個以上)。関数によって使用されるクエリパラメーターを宣言します。 指定した場合、関数の戻り値を定義する式の前に配置する必要があります。

> [!NOTE]
> クエリ "最上位" でサポートされている他の種類の [クエリステートメント](../statements.md) は、関数本体内ではサポートされていません。

### <a name="examples-of-user-defined-functions"></a>ユーザー定義関数の例 

**Let ステートメントを使用するユーザー定義関数**

次の例では、 `Test` 3 つの let ステートメントを使用するユーザー定義関数 (ラムダ) に名前をバインドします。 出力は `70` 次のとおりです。

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

次の例は、3つの引数を受け取る関数を示しています。 後者の2つには既定値があり、呼び出しサイトに存在する必要はありません。

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
print f(12, c=7) // Returns "12-b.default-7"
```

## <a name="invoking-a-user-defined-function"></a>ユーザー定義関数の呼び出し

引数を受け取らないユーザー定義関数は、名前または名前によって、またはかっこで囲まれた空の引数リストを使用して呼び出すことができます。

例 :

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

1つ以上のスカラー引数を受け取るユーザー定義関数は、テーブル名と、かっこ内の具象引数リストを使用して呼び出すことができます。

```kusto
let f=(a:string, b:string) {
  strcat(a, " (la la la)", b)
};
print f("hello", "world")
```

1つ以上のテーブル引数 (および任意の数のスカラー引数) を受け取るユーザー定義関数は、テーブル名とかっこ内の具象引数リストを使用して呼び出すことができます。

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v 
};
MyFilter((range x from 1 to 10 step 1), 9)
```

また、演算子を使用 `invoke` すると、1つ以上のテーブル引数を受け取り、テーブルを返すユーザー定義関数を呼び出すこともできます。 この関数は、関数の最初の具象テーブル引数が演算子のソースである場合に役立ち `invoke` ます。

```kusto
let append_to_column_a=(T:(a:string), what:string) {
    T | extend a=strcat(a, " ", what)
};
datatable (a:string) ["sad", "really", "sad"]
| invoke append_to_column_a(":-)")
```

## <a name="default-values"></a>既定の値

関数は、次の条件下で、一部のパラメーターに既定値を提供する場合があります。

* 既定値は、スカラーパラメーターに対してのみ指定できます。
* 既定値は常にリテラル (定数) です。 任意の計算を行うことはできません。
* 既定値が設定されていないパラメーターは、既定値を持つパラメーターの前に常に配置されます。
* 呼び出し元は、すべてのパラメーターの値を指定する必要があります。既定値は、関数宣言と同じ順序で配置されます。
* 呼び出し元は、既定値を持つパラメーターの値を指定する必要はありませんが、これを行うことができます。
* 呼び出し元は、パラメーターの順序と一致しない順序で引数を提供する場合があります。 その場合は、引数に名前を指定する必要があります。

次の例では、2つの同一レコードを含むテーブルを返します。 の最初の呼び出しでは、 `f` 引数は完全に "スクランブルされている" ため、それぞれに明示的に名前が付けられます。

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
|12-b. 既定-7|
|12-b. 既定-7|

## <a name="view-functions"></a>関数の表示

引数を取らず、表形式の式を返すユーザー定義関数は、 **ビュー**としてマークできます。 ユーザー定義関数をビューとしてマークすると、ワイルドカードテーブル名の解決が行われるたびに関数がテーブルのように動作することになります。
次の例では、とという2つのユーザー定義関数を示し `T_view` `T_notview` ています。また、のワイルドカード参照によって最初の関数のみが解決される方法を示してい `union` ます。

```kusto
let T_view = view () { print x=1 };
let T_notview = () { print x=2 };
union T*
```

## <a name="restrictions"></a>制限

次の制約が適用されます。

* ユーザー定義関数は、関数が呼び出される行コンテキストに依存する [toscalar ()](../toscalarfunction.md) 呼び出し情報に渡すことはできません。
* テーブル式を返すユーザー定義関数は、行コンテキストによって異なる引数を使用して呼び出すことはできません。
* リモートクラスターでは、少なくとも1つの表形式入力を取得する関数を呼び出すことはできません。
* リモートクラスターでは、スカラー関数を呼び出すことはできません。

行コンテキストによって異なる引数を使用してユーザー定義関数を呼び出すことができるのは、ユーザー定義関数がスカラー関数でのみ構成されており、使用されていない場合のみです `toscalar()` 。

**制限の例1**

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

**制限の例2**

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

完全を期すために、現在サポートされていないユーザー定義関数に関して一般的に要求される機能を次に示します。

1.  関数のオーバーロード: 現在、関数をオーバーロードする方法はありません (つまり、同じ名前と異なる入力スキーマを持つ複数の関数を作成します)。

2.  既定値: 関数に対するスカラーパラメーターの既定値は、スカラーリテラル (定数) である必要があります。 また、格納されている関数では、型の既定値を持つことはできません `dynamic` 。
