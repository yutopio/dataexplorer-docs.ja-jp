---
title: 動的なデータ型 - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer での動的なデータ型について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/09/2020
ms.localizationpriority: high
ms.openlocfilehash: 582683a9261d84fa24d819b5234e58effaf90a97
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512028"
---
# <a name="the-dynamic-data-type"></a>動的なデータ型

`dynamic` スカラー データ型は、配列とプロパティ バッグ以外にも、以下の一覧にある他のスカラー データ型の任意の値も使用できることが特長です。 具体的には、`dynamic` 値は次のようになります。

* Null。
* プリミティブ スカラー データ型の任意の値: `bool`、`datetime`、`guid`、`int`、`long`、`real`、`string`、および `timespan`。
* 0 から始まるインデックスを持つ、0 以上の値が保持される `dynamic` 値の配列。
* 一意の `string` 値を `dynamic` 値にマップするプロパティ バッグ。
  プロパティ バッグには、一意の `string` 値によってインデックス付けされたこのようなマッピング ("スロット" と呼ばれる) が 0 以上あります。 スロットは順序付けられていません。

> [!NOTE]
> * `dynamic` 型の値は、1 MB (2^20) に制限されます。
> * `dynamic` 型は JSON に似ていますが、JSON 内に存在しないため、JSON モデルによって表されない値を保持できます (`long`、`real`、`datetime`、`timespan`、`guid` など)。
>   したがって、`dynamic` 値を JSON 表現にシリアル化する場合、JSON によって表すことができない値が `string` 値にシリアル化されます。 逆に、Kusto では、文字列は厳密に型指定された値として解析されます (そのように解析できる場合)。
>   これは `datetime`、`real`、`long`、および `guid` 型に適用されます。 
>   JSON オブジェクト モデルの詳細については、「[json.org](https://json.org/)」を参照してください。
> * Kusto では、プロパティ バッグ内での名前と値のマッピングの順序の維持が試みられません。そのため、順序が維持されることを前提にはできません。 同じマッピング セットを持つ 2 つのプロパティ バッグでは、たとえば `string` 値として表されるときに、異なる結果が生成される可能性があります。

## <a name="dynamic-literals"></a>動的リテラル

`dynamic` 型のリテラルは次のようになります。

`dynamic(` *Value* `)`

*Value* は次のいずれかになります。

* `null`。リテラルが null の動的な値 `dynamic(null)` を表している場合。
* 別のスカラー データ型のリテラル。リテラルが "inner" 型の `dynamic` リテラルを表している場合。 たとえば、`dynamic(4)` は、long スカラー データ型の値 4 を保持する動的な値です。
* 動的またはその他のリテラルの配列:`[` *ListOfValues* `]`。 たとえば、`dynamic([1, 2, "hello"])` は 3 つの要素 (2 つの `long` 値と 1 つの `string` 値) から成る動的な配列です。
* プロパティ バッグ:`{` *Name* `=` *Value* ... `}`。 たとえば、`dynamic({"a":1, "b":{"a":2}})` は 2 つのスロット `a` および `b` を持つプロパティ バッグであり、2 番目のスロットは別のプロパティ バッグになっています。

```kusto
print o=dynamic({"a":123, "b":"hello", "c":[1,2,3], "d":{}})
| extend a=o.a, b=o.b, c=o.c, d=o.d
```

便宜上、クエリ テキスト自体に表示される `dynamic` リテラルには、`datetime`、`timespan`、`real`、`long`、`guid`、`bool`、および `dynamic` 型を持つ他の Kusto リテラルが含まれる場合もあります。
JSON に対するこの拡張機能は、文字列を解析する場合 (`parse_json` 関数を使用する場合やデータを取り込む場合など) には使用できませんが、次の操作を行うことができます。

```kusto
print d=dynamic({"a": datetime(1970-05-11)})
```

JSON エンコーディング規則に従う `string` 値を `dynamic` 値に解析するために、`parse_json` 関数を使用します。 次に例を示します。

* `parse_json('[43, 21, 65]')` - 数値の配列
* `parse_json('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` - ディクショナリ
* `parse_json('21')` - 数値を示す、動的な型の単一の値
* `parse_json('"21"')` - 文字列を示す、動的な型の単一の値
* `parse_json('{"a":123, "b":"hello", "c":[1,2,3], "d":{}}')` - 上記の例の `o` と同じ値を指定します。

> [!NOTE]
> JavaScript とは異なり、JSON では、文字列とプロパティ バッグのプロパティ名の前後に、二重引用符 (`"`) 文字を使用することが必須になっています。
> そのため、JSON 形式でエンコードされた文字列リテラルを引用符で囲むには、単一引用符 (`'`) 文字を使用する方が簡単です。
  
次の例では、`dynamic` 列 (および `datetime` 列) を保持するテーブルを定義して、1 つのレコードに取り込む方法を示します。 また、CSV ファイルで JSON 文字列をエンコードする方法も示します。

```kusto
// dynamic is just like any other type:
.create table Logs (Timestamp:datetime, Trace:dynamic)

// Everything between the "[" and "]" is parsed as a CSV line would be:
// 1. Since the JSON string includes double-quotes and commas (two characters
//    that have a special meaning in CSV), we must CSV-quote the entire second field.
// 2. CSV-quoting means adding double-quotes (") at the immediate beginning and end
//    of the field (no spaces allowed before the first double-quote or after the second
//    double-quote!)
// 3. CSV-quoting also means doubling-up every instance of a double-quotes within
//    the contents.
.ingest inline into table Logs
  [2015-01-01,"{""EventType"":""Demo"", ""EventValue"":""Double-quote love!""}"]
```

|Timestamp                   | Trace                                                 |
|----------------------------|-------------------------------------------------------|
|2015-01-01 00:00:00.0000000 | {"EventType":"Demo","EventValue":"Double-quote love!"}|

## <a name="dynamic-object-accessors"></a>動的オブジェクトのアクセサー

ディクショナリを添字で指定するには、ドット表記 (`dict.key`) または角かっこ表記 (`dict["key"]`) のいずれかを使用します。
添字が文字列定数の場合は、どちらのオプションも同等になります。

> [!NOTE] 
> 式を添字として使用するには、角かっこ表記を使用します。 算術式を使用する場合、角かっこ内の式はかっこで囲む必要があります。

次の例では、`dict` と `arr` は動的な型の列です。

|Expression                        | アクセサー式の型 | 説明                                                                              | 備考                                      |
|----------------------------------|--------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------|
|dict[col]                         | エンティティ名 (列)     | キーとして `col` 列の値を使用して、ディクショナリを添字で指定する              | 列は文字列型である必要があります                 | 
|arr[index]                        | エンティティ インデックス (列)    | インデックスとして `index` 列の値を使用して配列を添字で指定します              | 列は、整数型またはブール型である必要があります     | 
|arr[-index]                       | エンティティ インデックス (列)    | 配列の末尾から "インデックス" の位置の値を取得します                             | 列は、整数型またはブール型である必要があります     |
|arr[(-1)]                         | エンティティ インデックス             | 配列内の最後の値を取得します                                                |                                               |
|arr[toint(indexAsString)]         | 関数呼び出し            | 列 `indexAsString` の値を int にキャストし、それを使用して配列を添字で指定します |                                               |
|dict[['where']]                   | エンティティ名として使用されるキーワード (列) | キーとして `where` 列の値を使用して、ディクショナリを添字で指定します    | 一部のクエリ言語キーワードと同一のエンティティ名は、引用符で囲む必要があります | 
|dict.['where'] または dict['where']   | 定数                 | キーとして `where` 文字列を使用してディクショナリを添字で指定します                              |                                               |

**パフォーマンス ヒント:** 可能な場合は、定数の添字を使用することをお勧めします

`dynamic` 値のサブオブジェクトにアクセスすると、サブオブジェクトの基になる型が異なる場合でも、もう 1 つの `dynamic` 値が生成されます。 `gettype` 関数を使用して実際に値の基になっている型を検出し、以下の一覧にあるいずれかのキャスト関数によって実際の型にキャストします。

## <a name="casting-dynamic-objects"></a>動的オブジェクトをキャストする

> 動的オブジェクトを添字で指定した後に、値を単純な型にキャストする必要があります。

|Expression | 値 | Type|
|---|---|---|
| X | parse_json('[100,101,102]')| array|
|X[0]|parse_json('100')|動的|
|toint(X[1])|101| int|
| Y | parse_json('{"a1":100, "a b c":"2015-01-01"}')| ディクショナリ|
|Y.a1|parse_json('100')|動的|
|Y["a b c"]| parse_json("2015-01-01")|動的|
|todate(Y["a b c"])|datetime(2015-01-01)| datetime|

CAST 関数は次のとおりです。

* `tolong()`
* `todouble()`
* `todatetime()`
* `totimespan()`
* `tostring()`
* `toguid()`
* `todynamic()`

## <a name="building-dynamic-objects"></a>動的オブジェクトをビルドする

以下に示すいくつかの関数を使用して、新しい `dynamic` オブジェクトを作成できます。

* [pack()](../packfunction.md) は、名前と値のペアからプロパティ バッグを作成します。
* [pack_array()](../packarrayfunction.md) は、名前と値のペアから配列を作成します。
* [range()](../rangefunction.md) は、一連の算術数値を含む配列を作成します。
* [zip()](../zipfunction.md) は、2 つの配列から "並列" の値を組み合わせて、1 つの配列にします。
* [repeat()](../repeatfunction.md) は、繰り返し値を持つ配列を作成します。

さらに、集計値を保持する `dynamic` 配列を作成する集計関数がいくつかあります。

* [buildschema()](../buildschema-aggfunction.md) は、複数の `dynamic` 値の集計スキーマを返します。
* [make_bag()](../make-bag-aggfunction.md) は、グループ内の動的な値のプロパティ バッグを返します。
* [make_bag_if()](../make-bag-if-aggfunction.md) は、グループ内の動的な値のプロパティ バッグを (述語を使用して) 返します。
* [make_list()](../makelist-aggfunction.md) は、すべての値を保持する配列を順番に返します。
* [make_list_if()](../makelistif-aggfunction.md) は、すべての値を保持する配列を (述語を使用して) 順番に返します。
* [make_list_with_nulls()](../make-list-with-nulls-aggfunction.md) は、すべての値を保持する配列 (null 値を含む) を順番に返します。
* [make_set()](../makeset-aggfunction.md) は、すべての一意の値を保持する配列を返します。
* [make_set_if()](../makesetif-aggfunction.md) は、すべての一意の値を保持する配列を (述語を使用して) 返します。

## <a name="operators-and-functions-over-dynamic-types"></a>動的な型に対する演算子と関数

|演算子または関数|動的データ型の使用方法|
|---|---|
| *value* `in` *array*| *array* に *value* と等しい要素がある場合は true。<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| *array* に *value* と等しい要素がない場合は true。
|[`array_length(`array`)`](../arraylengthfunction.md)| 配列でない場合は null。
|[`bag_keys(`bag`)`](../bagkeysfunction.md)| 動的プロパティ バッグ オブジェクト内のすべてのルート キーを列挙します。
|[`bag_merge(`bag1,...,bagN`)`](../bag-merge-function.md)| 動的プロパティ バッグを、すべてのプロパティがマージされた動的プロパティ バッグにマージします。
|[`extractjson(`path,object`)`](../extractjsonfunction.md)|オブジェクトに移動するためのパスを使用します。
|[`parse_json(`ソース`)`](../parsejsonfunction.md)| JSON 文字列を動的オブジェクトに変換します。
|[`range(`from,to,step`)`](../rangefunction.md)| 値の配列。
|[`mv-expand` listColumn](../mvexpandoperator.md) | リスト内の指定されたセルの各値に対して、行を複製します。
|[`summarize buildschema(`column`)`](../buildschema-aggfunction.md) |列の内容から型スキーマを推測します。
|[`summarize make_bag(`column`)`](../make-bag-aggfunction.md) | 列内のプロパティ バッグ (ディクショナリ) 値を、キーの重複なしで、1 つのプロパティ バッグにマージします。
|[`summarize make_bag_if(`column,predicate`)`](../make-bag-if-aggfunction.md) | 列内のプロパティ バッグ (ディクショナリ) 値を、キーの重複なしで (述語を使用して)、1 つのプロパティ バッグにマージします。
|[`summarize make_list(`column`)` ](../makelist-aggfunction.md)| 行のグループをフラット化し、列の値を配列に格納します。
|[`summarize make_list_if(`column,predicate`)` ](../makelistif-aggfunction.md)| 行のグループをフラット化し、配列に列の値を (述語を使用して) 格納します。
|[`summarize make_list_with_nulls(`column`)` ](../make-list-with-nulls-aggfunction.md)| 行のグループをフラット化し、null 値を含め、配列に列の値を格納します。
|[`summarize make_set(`column`)`](../makeset-aggfunction.md) | 行のグループをフラット化し、列の値を重複しないように配列に格納します。
