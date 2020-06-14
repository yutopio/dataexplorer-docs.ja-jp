---
title: 動的データ型-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの動的データ型について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 606d48c4bda583ad82404a1b25119ec9beb4c5a9
ms.sourcegitcommit: 6f56b169fda0b74f9569004555a574d8973b1021
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/12/2020
ms.locfileid: "84748936"
---
# <a name="the-dynamic-data-type"></a>動的データ型

`dynamic`スカラーデータ型は、以下のリストにある他のスカラーデータ型の任意の値、および配列やプロパティバッグを受け取ることができることに特に適しています。 具体的には、値は次のように `dynamic` なります。

* 空白.
* プリミティブスカラーデータ型 `bool` (、、 `datetime` `guid` 、、 `int` `long` `real` `string` 、、、) `timespan` のいずれかの値。
* 0から始まる `dynamic` インデックス番号を持つ0個以上の値を保持している値の配列。
* 一意の `string` 値を値にマップするプロパティバッグ `dynamic` 。
  プロパティバッグには、一意の値によってインデックス付けされた0個以上のマッピング ("スロット" と呼ばれる) があり `string` ます。 スロットが順序付けられていません。

> [!NOTE]
> * 型の値 `dynamic` は、1 mb (2 ^ 20) に制限されます。
> * 型は `dynamic` json に似ていますが、json には存在しないため json モデルが表す値を保持できます (例:、、、、、 `long` `real` `datetime` `timespan` および `guid` )。
>   したがって、 `dynamic` 値を json 表現にシリアル化する場合、json で表すことができない値は値にシリアル化され `string` ます。 反対に、Kusto は文字列を厳密に型指定された値として解析します (そのように解析できる場合)。
>   これは、、、、およびの各型に適用さ `datetime` `real` `long` `guid` れます。 
>   JSON オブジェクトモデルの詳細については、「 [json.org](https://json.org/)」を参照してください。
> * Kusto は、プロパティバッグ内の名前から値へのマッピングの順序を維持しようとしません。そのため、保持する順序を想定することはできません。 同じマッピングのセットを持つ2つのプロパティバッグでは、たとえば、値として表現されるときに異なる結果を生成することができ `string` ます。

## <a name="dynamic-literals"></a>動的リテラル

型のリテラルは次の `dynamic` ようになります。

`dynamic(` *値* `)`

*値*は次のとおりです。

* `null`。この場合、リテラルは null 動的値を表します `dynamic(null)` 。
* 別のスカラーデータ型リテラル。この場合、リテラルは `dynamic` "inner" 型のリテラルを表します。 たとえば、 `dynamic(4)` は、long スカラーデータ型の値4を保持する動的な値です。
* 動的またはその他のリテラルの配列: `[` *listofvalues* `]` 。 たとえば、 `dynamic([1, 2, "hello"])` は、3つの要素 (2 つの `long` 値と1つの値) から成る動的配列です `string` 。
* プロパティバッグ: `{` *名前*の `=` *値*... `}` . たとえば、 `dynamic({"a":1, "b":{"a":2}})` は、2つのスロット、、およびを持つプロパティバッグで、 `a` `b` 2 番目のスロットは別のプロパティバッグです。

```kusto
print o=dynamic({"a":123, "b":"hello", "c":[1,2,3], "d":{}})
| extend a=o.a, b=o.b, c=o.c, d=o.d
```

便宜上、 `dynamic` クエリテキスト自体に表示されるリテラルには、他の Kusto リテラル (リテラル、リテラルなど) を含めることもでき `datetime` `timespan` ます。文字列を解析する場合 (関数を使用する場合やデータを取り込みする場合など)、JSON に対するこの拡張機能は使用できません `parse_json` が、次の操作を行うことができます。

```kusto
print d=dynamic({"a": datetime(1970-05-11)})
```

`string`JSON エンコーディング規則に従う値を値に解析するには、 `dynamic` 関数を使用し `parse_json` ます。 次に例を示します。

* `parse_json('[43, 21, 65]')` - 数値の配列
* `parse_json('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')`-辞書
* `parse_json('21')` - 数値を示す、動的な型の単一の値
* `parse_json('"21"')` - 文字列を示す、動的な型の単一の値
* `parse_json('{"a":123, "b":"hello", "c":[1,2,3], "d":{}}')`-上記の例と同じ値を指定し `o` ます。

> [!NOTE]
> JavaScript とは異なり、JSON では、 `"` 文字列とプロパティバッグプロパティ名の前後に二重引用符 () 文字を使用することを義務付けています。
> そのため、一般に、単一引用符 () 文字を使用して、JSON でエンコードされた文字列リテラルを簡単に引用 `'` できます。
  
次の例では、列と列を保持するテーブルを定義 `dynamic` `datetime` し、それに1つのレコードを取り込む方法を示します。 また、CSV ファイルで JSON 文字列をエンコードする方法も示します。

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
|2015-01-01 00:00: 00.0000000 | {"EventType": "Demo", "EventValue": "二重引用符が大好き!"}|

## <a name="dynamic-object-accessors"></a>動的オブジェクトアクセサー

辞書の添字を付けるには、ドット表記 ( `dict.key` ) または角かっこ () のいずれかを使用し `dict["key"]` ます。
添字が文字列定数の場合、両方のオプションが同じになります。

> [!NOTE] 
> 式を添字として使用するには、角かっこで囲んだ表記を使用します。 算術式を使用する場合、角かっこ内の式は、かっこで囲む必要があります。

次の例で `dict` は、と `arr` は動的な型の列です。

|正規表現                        | アクセサー式の型 | 意味                                                                              | コメント                                      |
|----------------------------------|--------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------|
|辞書 [col]                         | エンティティ名 (列)     | キーとして列の値を使用してディクショナリを添字する `col`              | 列は文字列型でなければなりません                 | 
|arr [インデックス]                        | エンティティインデックス (列)    | インデックスとして列の値を使用して配列の添字を指定する `index`              | 列は整数またはブール型である必要があります     | 
|arr [-index]                       | エンティティインデックス (列)    | 配列の末尾から ' index' 番目の値を取得します。                             | 列は整数またはブール型である必要があります     |
|arr [(-1)]                         | エンティティインデックス             | 配列内の最後の値を取得します。                                                |                                               |
|arr [toint (indexAsString)]         | 関数呼び出し            | 列の値を `indexAsString` int にキャストし、それらを使用して配列の添字を指定します。 |                                               |
|辞書 [[' where ']]                   | エンティティ名として使用されるキーワード (列) | キーとして列の値を使用してディクショナリの添字を指定する `where`    | 一部のクエリ言語キーワードと同一のエンティティ名は引用符で囲む必要があります | 
|辞書. [' where '] または辞書 [' where ']   | 定数                 | キーとして文字列を使用するディクショナリの添字 `where`                              |                                               |

**パフォーマンスのヒント:** 可能な場合は定数の添字を使用する

値のサブオブジェクトにアクセスすると `dynamic` `dynamic` 、サブオブジェクトの基になる型が異なる場合でも、別の値が得られます。 関数を使用して、 `gettype` 値の基になる実際の型を検出し、次に示すキャスト関数を実際の型にキャストします。

## <a name="casting-dynamic-objects"></a>動的オブジェクトのキャスト

> 動的オブジェクトを時した後、値を単純型にキャストする必要があります。

|正規表現 | 値 | 型|
|---|---|---|
| X | parse_json (' [100101102] ')| array|
|X [0]|parse_json (' 100 ')|動的|
|toint (X [1])|101| INT|
| Y | parse_json (' {"a1": 100, "a b c": "2015-01-01"} ')| ディクショナリ|
|X.y|parse_json (' 100 ')|動的|
|Y ["a b c"]| parse_json ("2015-01-01")|動的|
|年間累計 (Y ["a b c"])|datetime (2015-01-01)| DATETIME|

キャスト関数は次のとおりです。

* `tolong()`
* `todouble()`
* `todatetime()`
* `totimespan()`
* `tostring()`
* `toguid()`
* `todynamic()`

## <a name="building-dynamic-objects"></a>動的オブジェクトの構築

いくつかの関数を使用すると、新しいオブジェクトを作成でき `dynamic` ます。

* [pack ()](../packfunction.md)は、名前と値のペアからプロパティバッグを作成します。
* [pack_array ()](../packarrayfunction.md)は、名前と値のペアから配列を作成します。
* [range ()](../rangefunction.md)は、数値の算術系列を含む配列を作成します。
* [zip ()](../zipfunction.md)は、2つの配列の "parallel" 値を1つの配列に組み合わせます。
* [repeat ()](../repeatfunction.md)は、値を繰り返す配列を作成します。

さらに、集計 `dynamic` 値を保持する配列を作成する集計関数がいくつかあります。

* [buildschema ()](../buildschema-aggfunction.md)は、複数の値の集計スキーマを返し `dynamic` ます。
* [make_bag ()](../make-bag-aggfunction.md)は、グループ内の動的な値のプロパティバッグを返します。
* [make_bag_if ()](../make-bag-if-aggfunction.md)は、グループ内の動的な値のプロパティバッグを返します (述語を含む)。
* [make_list ()](../makelist-aggfunction.md)は、すべての値を含む配列を順番に返します。
* [make_list_if ()](../makelistif-aggfunction.md)は、すべての値を含む配列を (述語を含む) 順番に返します。
* [make_list_with_nulls ()](../make-list-with-nulls-aggfunction.md)は、null 値を含むすべての値を順番に保持する配列を返します。
* [make_set ()](../makeset-aggfunction.md)は、すべての一意の値を保持する配列を返します。
* [make_set_if ()](../makesetif-aggfunction.md)は、すべての一意の値を保持する配列を返します (述語を含む)。

## <a name="operators-and-functions-over-dynamic-types"></a>動的な型に対する演算子と関数

|||
|---|---|
| *value* `in` *array*| *array* に *value* と等しい要素がある場合は true。<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| *array* に *value* と等しい要素がない場合は true。
|[`array_length(`配列`)`](../arraylengthfunction.md)| 配列でない場合は null。
|[`bag_keys(`bag`)`](../bagkeysfunction.md)| 動的プロパティバッグオブジェクト内のすべてのルートキーを列挙します。
|[`extractjson(`path、object`)`](../extractjsonfunction.md)|オブジェクトに移動するためのパスを使用します。
|[`parse_json(`電源`)`](../parsejsonfunction.md)| JSON 文字列を動的オブジェクトに変換します。
|[`range(`from、to、step`)`](../rangefunction.md)| 値の配列。
|[`mv-expand`listColumn](../mvexpandoperator.md) | リスト内の指定されたセルの各値に対して、行を複製します。
|[`summarize buildschema(`項目`)`](../buildschema-aggfunction.md) |列の内容から型スキーマを推測します。
|[`summarize make_bag(`項目`)`](../make-bag-aggfunction.md) | 列のプロパティバッグ (dictionary) 値を、キーの重複なしで1つのプロパティバッグにマージします。
|[`summarize make_bag_if(`列、述語`)`](../make-bag-if-aggfunction.md) | 列のプロパティバッグ (ディクショナリ) 値を1つのプロパティバッグにマージします。キーの重複はありません (述語を含む)。
|[`summarize make_list(`列 `)`](../makelist-aggfunction.md)| 行のグループをフラット化し、列の値を配列に格納します。
|[`summarize make_list_if(`列、述語 `)`](../makelistif-aggfunction.md)| 行のグループを平坦化し、列の値を配列に格納します (述語を含む)。
|[`summarize make_list_with_nulls(`列 `)`](../make-list-with-nulls-aggfunction.md)| 行のグループを平坦化し、null 値を含む、列の値を配列に格納します。
|[`summarize make_set(`項目`)`](../makeset-aggfunction.md) | 行のグループをフラット化し、列の値を重複しないように配列に格納します。
|[`summarize make_bag(`項目`)`](../make-bag-aggfunction.md) | 列のプロパティバッグ (dictionary) 値を、キーの重複なしで1つのプロパティバッグにマージします。
