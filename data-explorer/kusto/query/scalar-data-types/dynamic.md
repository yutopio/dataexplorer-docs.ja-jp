---
title: 動的データ型 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの動的データ型について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: e1cdb6e5af20b326198a7447c50c24e5f632d237
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509879"
---
# <a name="the-dynamic-data-type"></a>動的データ型

ス`dynamic`カラー データ型は、配列やプロパティ バッグだけでなく、以下のリストから他のスカラー データ型の値を使用できるという特に特殊なデータ型です。 具体的には、`dynamic`次の値を指定できます。

* Null。
* プリミティブ スカラー データ型の値。 `bool` `datetime` `guid` `int` `long` `real` `string` `timespan`
* 0`dynamic`から始まるインデックスを持つ 0 個以上の値を保持する値の配列。
* 一意`string`の値を値にマップ`dynamic`するプロパティ バッグ。
  プロパティ バッグには、一意`string`の値によってインデックスが付いた、ゼロ以上のこのようなマッピング (「スロット」と呼ばれます) があります。 スロットは順序付けされていません。

> [!NOTE]
> 型`dynamic`の値は 1 MB (2^20) に制限されます。

> [!NOTE]
> 型は`dynamic`JSON に似`long`ていますが、JSON モデルが表さない値を保持できます( たとえば、 `real` `datetime` `timespan`、、、、、、、、、、) `guid`。
> したがって、値を`dynamic`JSON 表現にシリアル化する場合、JSON が表す値を表`string`す値はシリアル化されます。 逆に、文字列を厳密に型指定された値として解析する場合は、文字列を解析します。
> これは、 `datetime`、 `real` `long`、および`guid`の各型に適用されます。 JSON オブジェクト モデルの詳細については、「 [json.org」](https://json.org/)を参照してください。

> [!NOTE]
> Kusto はプロパティ バッグ内の名前と値のマッピングの順序を保持しようとはしません。 同じマッピング セットを持つ 2 つのプロパティ バッグが値として`string`表される場合など、結果が異なる場合は、完全に可能です。

## <a name="dynamic-literals"></a>動的リテラル

型`dynamic`のリテラルは次のようになります。

`dynamic(` *値* `)`

*値*は次の場合があります。

* `null`この場合、リテラルは null 動的値を表`dynamic(null)`します。
* 別のスカラー データ型リテラル (この場合、リテラル`dynamic`は "内部" 型のリテラルを表します)。 たとえば、`dynamic(4)`長いスカラー データ型の値 4 を保持する動的な値です。
* 動的リテラルまたはその他のリテラルの配列: `[` *ListOfValues* `]`. たとえば、3`dynamic([1, 2, "hello"])`つの要素、2 つの`long`値と 1`string`つの値を持つ動的な配列です。
* プロパティバッグ: `{` *名前*`=`*の値*..`}`. たとえば、2`dynamic({"a":1, "b":{"a":2}})`つのスロットと 、`a``b`が 2 つのスロットを持つプロパティ バッグで、2 番目のスロットは別のプロパティ バッグです。

```kusto
print o=dynamic({"a":123, "b":"hello", "c":[1,2,3], "d":{}})
| extend a=o.a, b=o.b, c=o.c, d=o.d
```

便宜上`dynamic`、クエリ テキスト自体に表示されるリテラルには、他の Kusto リテラル (`datetime`リテラル、`timespan`リテラルなど) も含めることができます。JSON 上のこの拡張機能は、(関数を使用するときやデータを`parse_json`取り込むときなど) 文字列を解析するときには使用できませんが、これを行うことができます。

```kusto
print d=dynamic({"a": datetime(1970-05-11)})
```

JSON エンコーディング`string`規則に従う値を解析して値に`dynamic`変換するには、関数`parse_json`を使用します。 次に例を示します。

* `parse_json('[43, 21, 65]')` - 数値の配列
* `parse_json('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')`- 辞書
* `parse_json('21')` - 数値を示す、動的な型の単一の値
* `parse_json('"21"')` - 文字列を示す、動的な型の単一の値
* `parse_json('{"a":123, "b":"hello", "c":[1,2,3], "d":{}}')`- 上記の例と`o`同じ値を指定します。

> [!NOTE]
> JavaScript とは異なり、JSON では文字列とプロパティ`"`バッグプロパティ名の前後に二重引用符 () 文字を使用する必要があります。
> したがって、一般に、JSON エンコードされた文字列リテラルを単一引用符 ()`'`文字を使用して引用する方が簡単です。
  
次の例は、列と列`dynamic``datetime`を保持するテーブルを定義し、そのテーブルに 1 つのレコードを取り込む方法を示しています。 また、JSON 文字列を CSV ファイルにエンコードする方法も示しています。

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
|2015-01-01 00:00:00.0000000 | {"イベントタイプ":"デモ","イベント値":"二重引用符愛!"}|

## <a name="dynamic-object-accessors"></a>動的オブジェクト アクセサー

辞書を下付き文字にするには、ドット表記 (`dict.key`) または角かっこ (`dict["key"]`) を使用します。
添字が文字列定数の場合、両方のオプションは同じです。

> [!NOTE] 
> 式を添字として使用するには、角かっこ表記を使用します。 算術式を使用する場合、括弧内の式は括弧で囲む必要があります。

以下`dict`の例では、`arr`動的型の列です。

|式                        | アクセサ式の型 | 意味                                                                              | 説明                                      |
|----------------------------------|--------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------|
|ディクスト[コル]                         | エンティティ名 (列)     | キーとして列`col`の値を使用して辞書を下付け              | 列は文字列型である必要があります                 | 
|arr[インデックス]                        | エンティティインデックス (列)    | 列`index`の値をインデックスとして使用して配列を添字化する              | 列は整数またはブール型でなければなりません     | 
|arr[-インデックス]                       | エンティティインデックス (列)    | 配列の末尾から 'index'番目の値を取得します。                             | 列は整数またはブール型でなければなりません     |
|arr[(-1)]                         | エンティティ インデックス             | 配列内の最後の値を取得します。                                                |                                               |
|arr[トイント(インデックスアスストリング)         | 関数呼び出し            | 列`indexAsString`の値を int にキャストし、配列を添字化するために使用します。 |                                               |
|ディクスト[[場所]]                   | エンティティ名 (列) として使用されるキーワード | 列`where`の値をキーとして使用して辞書を下付きする    | 一部のクエリ言語キーワードと同一のエンティティ名は、引用符で囲む必要があります。 | 
|ディクスト.[場所]またはディクスト[場所]]   | 定数                 | 文字列をキーとして使用`where`して辞書を下付けする                              |                                               |

**パフォーマンスのヒント:** 可能な場合は定数添え字を使用することを好む

`dynamic`値のサブオブジェクトにアクセスすると、サブオブジェクトの`dynamic`基になる型が異なる場合でも、別の値が得られます。 この関数`gettype`を使用して、値の実際の基になる型を検出し、以下に示すキャスト関数のいずれかを使用して実際の型にキャストします。

## <a name="casting-dynamic-objects"></a>動的オブジェクトのキャスト

> 動的オブジェクトを添字化した後、値を単純な型にキャストする必要があります。

|式 | 値 | Type|
|---|---|---|
| X | parse_json('[100,101,102])| array|
|X[0]|parse_json('100')|動的|
|トイント(X[1])|101| INT|
| Y | parse_json('{"a1":100,"b c":"2015-01-01"})| ディクショナリ|
|Y.a1|parse_json('100')|動的|
|Y[a b c"]| parse_json(「2015-01-01」)|動的|
|トデイト(Y["a b c"))|日時(2015-01-01)| DATETIME|

キャスト関数は次のとおりです。

* `tolong()`
* `todouble()`
* `todatetime()`
* `totimespan()`
* `tostring()`
* `toguid()`
* `todynamic()`

## <a name="building-dynamic-objects"></a>動的オブジェクトの構築

新しい`dynamic`オブジェクトを作成するための関数がいくつかあります。

* [pack() は](../packfunction.md)、名前と値のペアからプロパティ バッグを作成します。
* [pack_array() は](../packarrayfunction.md)、名前と値のペアから配列を作成します。
* [range() は](../rangefunction.md)、数値の算術系列を持つ配列を作成します。
* [zip() は](../zipfunction.md)、2 つの配列の 「並列」値を 1 つの配列にペア化します。
* [repeat() は](../repeatfunction.md)、繰り返し値を持つ配列を作成します。

さらに、集計値を保持する配列を作成`dynamic`する集計関数が複数あります。

* [buildschema() は](../buildschema-aggfunction.md)複数`dynamic`の値の集計スキーマを返します。
* [make_bag() は](../make-bag-aggfunction.md)、グループ内の動的な値のプロパティ バッグを返します。
* [make_bag_if() は](../make-bag-if-aggfunction.md)、グループ内の動的な値のプロパティ バッグを返します (述語を含む)。
* [make_list() は](../makelist-aggfunction.md)、すべての値を保持する配列を順番に返します。
* [make_list_if() は](../makelistif-aggfunction.md)、すべての値を保持する配列を順に返します (述語を含む)。
* [make_list_with_nulls() は](../make-list-with-nulls-aggfunction.md)、null 値を含むすべての値を順番に保持する配列を返します。
* [make_set() は](../makeset-aggfunction.md)、すべての一意の値を保持する配列を返します。
* [make_set_if() は](../makesetif-aggfunction.md)、すべての一意の値を保持する配列を返します (述語を含む)。

## <a name="operators-and-functions-over-dynamic-types"></a>動的な型に対する演算子と関数

|||
|---|---|
| *value* `in` *array*| *array* に *value* と等しい要素がある場合は true。<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| *array* に *value* と等しい要素がない場合は true。
|[`array_length(`配列`)`](../arraylengthfunction.md)| 配列でない場合は null。
|[`bag_keys(`バッグ`)`](../bagkeysfunction.md)| 動的プロパティ バッグ オブジェクト内のすべてのルート キーを列挙します。
|[`extractjson(`パス,オブジェクト`)`](../extractjsonfunction.md)|オブジェクトに移動するためのパスを使用します。
|[`parse_json(`ソース`)`](../parsejsonfunction.md)| JSON 文字列を動的オブジェクトに変換します。
|[`range(`から、へ、ステップ`)`](../rangefunction.md)| 値の配列。
|[`mv-expand`リスト列](../mvexpandoperator.md) | リスト内の指定されたセルの各値に対して、行を複製します。
|[`summarize buildschema(`列`)`](../buildschema-aggfunction.md) |列の内容から型スキーマを推測します。
|[`summarize make_bag(`列`)`](../make-bag-aggfunction.md) | 列のプロパティ バッグ (ディクショナリ) 値を、キーの重複なしに 1 つのプロパティ バッグにマージします。
|[`summarize make_bag_if(`列,述語`)`](../make-bag-if-aggfunction.md) | 列のプロパティ バッグ (ディクショナリ) 値を 1 つのプロパティ バッグにマージします。
|[`summarize make_list(`列`)`](../makelist-aggfunction.md)| 行のグループをフラット化し、列の値を配列に格納します。
|[`summarize make_list_if(`列,述`)`語](../makelistif-aggfunction.md)| 行のグループを平坦化し、列の値を配列に格納します (述語付き)。
|[`summarize make_list_with_nulls(`列`)`](../make-list-with-nulls-aggfunction.md)| 行のグループを平坦化し、列の値を配列に格納します (null 値を含む)。
|[`summarize make_set(`列`)`](../makeset-aggfunction.md) | 行のグループをフラット化し、列の値を重複しないように配列に格納します。
|[`summarize make_bag(`列`)`](../make-bag-aggfunction.md) | 列のプロパティ バッグ (ディクショナリ) 値を、キーの重複なしに 1 つのプロパティ バッグにマージします。