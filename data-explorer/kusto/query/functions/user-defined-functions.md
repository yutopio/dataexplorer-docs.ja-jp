---
title: ユーザー定義関数 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのユーザー定義関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: e8372be303b1540e1421f226ed0fd0fe74d44545
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610216"
---
# <a name="user-defined-functions"></a>ユーザー定義関数

Kusto は、次のいずれかのユーザー定義関数をサポートしています。
* クエリ自体の一部 (**アドホック関数**) 
* データベースメタデータの一部として永続的に保存される (**ストアドファンクション**)

ユーザー定義関数には、次のものがあります。
* 名前:
    * [識別子の命名規則](../schema-entities/entity-names.md#identifier-naming-rules)に従う必要があります
    * 型仕様を持つ定義のスコープ内で一意である必要があります。
* 入力パラメータの厳密に型指定されたリスト:
    * スカラー式または表形式式の場合があります。
    * スカラー パラメーターは既定値を指定でき、関数の呼び出し元がパラメーターの値を指定しない場合に暗黙的に使用できます (以下の[「デフォルト値](#default-values)」を参照)。
* 厳密に型指定された戻り値 (スカラーまたは表形式の場合があります)

関数の入力と出力によって、関数の使用方法と使用方法が決まります。

* **スカラー関数**: 
    * 入力のない関数、またはスカラー入力のみで、スカラー出力を生成する関数
    * スカラー式が許可されている場所であればどこでも使用できます。
    * 定義されている行コンテキストのみを使用する場合があります。
    * アクセス可能なスキーマ内のテーブル (およびビュー) のみを参照できます。

例:

```kusto
let Add7 = (arg0:long = 5) { arg0 + 7 };
range x from 1 to 10 step 1
| extend x_plus_7 = Add7(x), five_plus_seven = Add7()
```

* **表形式関数**: 
    * 入力のない関数、または少なくとも 1 つの表形式入力を指定し、表形式の出力を生成します。
    * 表形式の式が許可されている場合はどこでも使用できます。 

> [!NOTE]
> すべての表形式パラメーターは、スカラー パラメーターの前に指定する必要があります。

引数を取らない表形式関数の例:

```kusto
let tenNumbers = () { range x from 1 to 10 step 1};
tenNumbers
| extend x_plus_7 = x + 7
```

表形式の入力とスカラー入力を使用する表形式関数の例を次に示します。

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

列が指定されていない表形式の入力を使用する表形式関数の例。 関数に任意のテーブルを渡すことができますし、関数内でテーブルの列を参照することはできません。

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

ユーザー定義関数の宣言は、次の機能を提供します。

* 関数**名**
* 関数**スキーマ**(受け入れるパラメーターがある場合)
* 関数**本体**

> [!Note]
> オーバーロード関数はサポートされていません。 たとえば、同じ名前と異なる入力スキーマを持つ複数の関数を作成することはできません。

> [!TIP]
> ラムダ関数は名前を持たないし[、let ステートメント](../letstatement.md)を使用して名前にバインドされます。 したがって、ユーザー定義のストアド関数と見なすことができます。
> 例: `string` 2 つの引数 (呼び出しと呼`s`び出`long``i`し) を受け取るラムダ関数の宣言。 最初の積 (数値に変換した後) と 2 番目の積を返します。 ラムダは名前`f`にバインドされます。

```kusto
let f=(s:string, i:long) {
    tolong(s) * i
};
```

関数本体には、次の**項目**が含まれます。

* 関数の戻り値 (スカラーまたは表形式の値) を提供する式が 1 つだけ返されます。
* [let ステートメント](../letstatement.md)の任意の数 (ゼロ以上) で、スコープは関数本体のスコープです。 指定した場合、let ステートメントは関数の戻り値を定義する式の前に置く必要があります。
* 関数で使用されるクエリ パラメーターを宣言する[クエリ パラメーター ステートメント](../queryparametersstatement.md)の任意の数 (0 個以上)。 指定した場合は、関数の戻り値を定義する式の前に指定する必要があります。

> [!NOTE]
> クエリ "最上位" でサポートされている他の種類の[クエリ ステートメント](../statements.md)は、関数本体内ではサポートされていません。

### <a name="examples-of-user-defined-functions"></a>ユーザー定義関数の例 

**let ステートメントを使用するユーザー定義関数**

次の例では、名前`Test`を 3 つの let ステートメントを使用するユーザー定義関数 (ラムダ) にバインドします。 出力は次`70`のとおりです。

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

**パラメーターのデフォルト値を定義するユーザー定義関数**

次の例は、3 つの引数を受け取る関数を示しています。 後者の 2 つは既定値を持ち、呼び出しサイトに存在する必要はありません。

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
print f(12, c=7) // Returns "12-b.default-7"
```

## <a name="invoking-a-user-defined-function"></a>ユーザー定義関数の呼び出し

引数を取らないユーザー定義関数は、その名前または名前によって、または括弧内に空の引数リストを含む名前によって呼び出すことができます。

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

1 つ以上のスカラー引数を受け取るユーザー定義関数は、テーブル名と具象引数リストを括弧で囲んで呼び出すことができます。

```kusto
let f=(a:string, b:string) {
  strcat(a, " (la la la)", b)
};
print f("hello", "world")
```

1 つ以上のテーブル引数 (および任意の数のスカラー引数) を受け取るユーザー定義関数は、テーブル名と、括弧内の具象引数リストを使用して呼び出すことができます。

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v 
};
MyFilter((range x from 1 to 10 step 1), 9)
```

また、この演算子`invoke`を使用して、1 つ以上のテーブル引数を受け取ってテーブルを返すユーザー定義関数を呼び出すこともできます。 これは、関数に対する最初の具象テーブル引数が演算子のソースである`invoke`場合に便利です。

```kusto
let append_to_column_a=(T:(a:string), what:string) {
    T | extend a=strcat(a, " ", what)
};
datatable (a:string) ["sad", "really", "sad"]
| invoke append_to_column_a(":-)")
```

## <a name="default-values"></a>既定の値

関数は、次の条件の下で、パラメーターの一部に既定値を提供します。

* スカラー パラメーターに対してのみ既定値を指定できます。
* 既定値は常にリテラル (定数) です。 任意の計算をすることはできません。
* 既定値のないパラメーターは、常に既定値を持つパラメーターの前に置きます。
* 呼び出し元は、関数宣言と同じ順序で配置された既定値を持たないすべてのパラメーターの値を提供する必要があります。
* 呼び出し元は、既定値を持つパラメーターの値を指定する必要はありませんが、この場合は指定できます。
* 呼び出し元は、パラメーターの順序と一致しない順序で引数を指定できます。 その場合は、引数に名前を付ける必要があります。

次の例では、2 つの同一レコードを含むテーブルを返します。 の最初の`f`呼び出しでは、引数は完全に "スクランブル" されるため、各引数には明示的に名前が付けられます。

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

## <a name="view-functions"></a>関数の表示

引数を受け取らず、表形式の式を返すユーザー定義関数は **、 ビュー**としてマークできます。 ユーザー定義関数をビューとしてマークすると、ワイルドカード テーブル名の解決が行われるたびに、関数はテーブルのように動作します。
次の例は、2 つのユーザー定義`T_view`関数`T_notview`と、 のワイルドカード参照によって最初の関数だけが解決される方法を`union`示しています。

```kusto
let T_view = view () { print x=1 };
let T_notview = () { print x=2 };
union T*
```

## <a name="user-defined-functions-usage-restrictions"></a>ユーザー定義関数の使用制限

次の制限事項が適用されます。

* ユーザー定義関数は、関数が呼び出される行コンテキストに依存する[toscalar()](../toscalarfunction.md)呼び出し情報に渡す必要はありません。
* 表形式の式を返すユーザー定義関数は、行コンテキストによって異なる引数を使用して呼び出す必要があります。
* 少なくとも 1 つの表形式の入力を受け取る関数は、リモート クラスターで呼び出せません。
* スカラー関数は、リモート クラスターでは呼び出しできません。

行コンテキストによって異なる引数を使用してユーザー定義関数を呼び出す唯一の場所は、ユーザー定義関数がスカラー関数のみで構成され、使用`toscalar()`しない場合です。

**制限 1 の例**

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

**制限 2 の例**

```kusto
// Not supported:
// f is a tabular function that is invoked in a context
// that expects a scalar value.
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { range x from 1 to hours step 1 | summarize make_list(x) };
Table2 | where Column != 123 | project d = f(Column)
```
