---
title: データマッピング-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのデータマッピングについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 2a3b402c04d5d1af85b2c2a042a23fbade7e2524
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617648"
---
# <a name="data-mappings"></a>データのマッピング

取り込み中にデータマッピングを使用して、受信データを Kusto テーブル内の列にマップします。

Kusto では、さまざまな種類の`row-oriented`マッピング (CSV、JSON、AVRO) と`column-oriented` (Parquet) がサポートされています。

マッピングリストの各要素は、次の3つのプロパティから構成されます。

|プロパティ|説明|
|----|--|
|`column`|Kusto テーブルのターゲット列名|
|`datatype`| Optionalマップされた列が Kusto テーブルにまだ存在しない場合に作成するデータ型|
|`Properties`|Optional次の各セクションで説明するように、各マッピングに固有のプロパティを含むプロパティバッグ。


すべてのマッピングは[事前に作成](create-ingestion-mapping-command.md)でき、パラメーターを使用して`ingestionMappingReference`インジェストコマンドから参照できます。

## <a name="csv-mapping"></a>CSV マッピング

ソースファイルが CSV (または任意の区切り記号区切り形式) で、そのスキーマが現在の Kusto テーブルスキーマと一致しない場合、CSV マッピングはファイルスキーマから Kusto テーブルスキーマにマップされます。 テーブルが Kusto に存在しない場合は、このマッピングに従って作成されます。 テーブルにマッピングの一部のフィールドがない場合は、それらが追加されます。 

CSV マッピングは、CSV、TSV、PSV、SCSV、SOHsv のすべての区切り記号で区切られた形式で適用できます。

リスト内の各要素には、特定の列のマッピングが記述され、次のプロパティが含まれる場合があります。

|プロパティ|説明|
|----|--|
|`ordinal`|CSV の列の順序番号|
|`constantValue`|OptionalCSV 内の一部の値ではなく、列に使用される定数値|

> [!NOTE]
> `Ordinal`と`ConstantValue`は相互に排他的です。

### <a name="example-of-the-csv-mapping"></a>CSV マッピングの例

``` json
[
  { "column" : "rownumber", "Properties":{"Ordinal":"0"}},
  { "column" : "rowguid",   "Properties":{"Ordinal":"1"}},
  { "column" : "xdouble",   "Properties":{"Ordinal":"2"}},
  { "column" : "xbool",     "Properties":{"Ordinal":"3"}},
  { "column" : "xint32",    "Properties":{"Ordinal":"4"}},
  { "column" : "xint64",    "Properties":{"Ordinal":"5"}},
  { "column" : "xdate",     "Properties":{"Ordinal":"6"}},
  { "column" : "xtext",     "Properties":{"Ordinal":"7"}},
  { "column" : "const_val", "Properties":{"ConstValue":"Sample: constant value"}}
]
```

> [!NOTE]
> 上記のマッピングを`.ingest`制御コマンドの一部として指定すると、JSON 文字列としてシリアル化されます。

* 上記のマッピングが[事前に作成](create-ingestion-mapping-command.md)されている場合、 `.ingest`コントロールコマンドで参照できます。

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMappingReference = "Mapping1"
    )
```

* 上記のマッピングを`.ingest`制御コマンドの一部として指定すると、JSON 文字列としてシリアル化されます。

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMapping = 
        "["
            "{\"column\":\"rownumber\",\"Properties\":{\"Ordinal\":0}},"
            "{\"column\":\"rowguid\",  \"Properties\":{\"Ordinal\":1}}"
        "]" 
    )
```

**注:** 次のマッピング形式`Properties`は、現在サポートされていますが、今後非推奨とされる可能性があります。

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMapping = 
        "["
            "{\"column\":\"rownumber\",\"Ordinal\": 0},"
            "{\"column\":\"rowguid\",  \"Ordinal\": 1}"
        "]" 
    )
```

## <a name="json-mapping"></a>JSON マッピング

ソースファイルが JSON 形式の場合、ファイルの内容は Kusto テーブルにマップされます。 マップされているすべての列に有効なデータ型が指定されていない限り、テーブルは Kusto データベースに存在する必要があります。 すべての既存でない列にデータ型が指定されていない限り、JSON マッピングでマップされている列は、Kusto テーブルに存在する必要があります。

リスト内の各要素には、特定の列のマッピングが記述され、次のプロパティが含まれる場合があります。 

|プロパティ|説明|
|----|--|
|`path`|がで`$`始まる場合: json ドキュメント内の列のコンテンツになるフィールドへの json パス (ドキュメント全体が`$`であることを示す json パス)。 値が次の値で`$`始まらない場合: 定数値が使用されます。|
|`transform`|Optional[マッピング変換](#mapping-transformations)を使用してコンテンツに適用する必要がある変換。|

### <a name="example-of-json-mapping"></a>JSON マッピングの例

```json
[
  { "column" : "rownumber",   "Properties":{"Path":"$.rownumber"}}, 
  { "column" : "rowguid",     "Properties":{"Path":"$.rowguid"}}, 
  { "column" : "xdouble",     "Properties":{"Path":"$.xdouble"}}, 
  { "column" : "xbool",       "Properties":{"Path":"$.xbool"}}, 
  { "column" : "xint32",      "Properties":{"Path":"$.xint32"}}, 
  { "column" : "xint64",      "Properties":{"Path":"$.xint64"}}, 
  { "column" : "xdate",       "Properties":{"Path":"$.xdate"}}, 
  { "column" : "xtext",       "Properties":{"Path":"$.xtext"}}, 
  { "column" : "location",    "Properties":{"transform":"SourceLocation"}}, 
  { "column" : "lineNumber",  "Properties":{"transform":"SourceLineNumber"}}, 
  { "column" : "full_record", "Properties":{"Path":"$"}}
]
```

> [!NOTE]
> 上記のマッピングを`.ingest`制御コマンドの一部として指定すると、JSON 文字列としてシリアル化されます。

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="json", 
        ingestionMappingReference = "Mapping1"
    )
```

**注:** 次のマッピング形式`Properties`は、現在サポートされていますが、今後非推奨とされる可能性があります。

```kusto
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "json", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"path\":\"$.rownumber\"},"
        "{\"column\":\"rowguid\",  \"path\":\"$.rowguid\"}"
      "]"
  )
```
    
## <a name="avro-mapping"></a>Avro マッピング

ソースファイルが Avro 形式の場合、Avro ファイルの内容が Kusto テーブルにマップされます。 マップされているすべての列に有効なデータ型が指定されていない限り、テーブルは Kusto データベースに存在する必要があります。 Avro マッピングでマップされている列は、すべての既存でない列にデータ型が指定されていない限り、Kusto テーブルに存在する必要があります。

リスト内の各要素には、特定の列のマッピングが記述され、次のプロパティが含まれる場合があります。 

|プロパティ|説明|
|----|--|
|`Field`|Avro レコードのフィールドの名前。|
|`Path`|を使用する`field`代わりに、必要に応じて Avro レコードフィールドの内部部分を取得することもできます。 値は、レコードのルートからの JSON パスを表します。 詳細については、以下のメモを参照してください。 |
|`transform`|Optional[サポートされている変換](#mapping-transformations)を使用してコンテンツに適用する必要がある変換。|

**メモ**
>[!NOTE]
> * `field`と`path`を一緒に使用することはできません。使用できるのは1つだけです。 
> * `path`ルート`$`のみを指すことはできません。パスのレベルが少なくとも1つ必要です。

以下の2つの選択肢は同じです。

``` json
[
  {"column": "rownumber", "Properties":{"Path":"$.RowNumber"}}
]
```

``` json
[
  {"column": "rownumber", "Properties":{"Field":"RowNumber"}}
]
```

### <a name="example-of-the-avro-mapping"></a>AVRO マッピングの例

``` json
[
  {"column": "rownumber", "Properties":{"Field":"rownumber"}},
  {"column": "rowguid",   "Properties":{"Field":"rowguid"}},
  {"column": "xdouble",   "Properties":{"Field":"xdouble"}},
  {"column": "xboolean",  "Properties":{"Field":"xboolean"}},
  {"column": "xint32",    "Properties":{"Field":"xint32"}},
  {"column": "xint64",    "Properties":{"Field":"xint64"}},
  {"column": "xdate",     "Properties":{"Field":"xdate"}},
  {"column": "xtext",     "Properties":{"Field":"xtext"}}
]
``` 

> [!NOTE]
> 上記のマッピングを`.ingest`制御コマンドの一部として指定すると、JSON 文字列としてシリアル化されます。

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="avro", 
        ingestionMappingReference = "Mapping1"
    )
```

**注:** 次のマッピング形式`Properties`は、現在サポートされていますが、今後非推奨とされる可能性があります。

```kusto
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "avro", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"field\":\"rownumber\"},"
        "{\"column\":\"rowguid\",  \"field\":\"rowguid\"}"
      "]"
  )
```

## <a name="parquet-mapping"></a>Parquet のマッピング

ソースファイルが Parquet 形式の場合、ファイルの内容は Kusto テーブルにマップされます。 マップされているすべての列に有効なデータ型が指定されていない限り、テーブルは Kusto データベースに存在する必要があります。 Parquet マッピングでマップされている列は、すべての既存でない列にデータ型が指定されていない限り、Kusto テーブルに存在する必要があります。

リスト内の各要素には、特定の列のマッピングが記述され、次のプロパティが含まれる場合があります。

|プロパティ|説明|
|----|--|
|`path`|がで`$`始まる場合: Parquet ドキュメント内の列のコンテンツになるフィールドへの json パス (ドキュメント全体が`$`であることを示す json パス)。 値が次の値で`$`始まらない場合: 定数値が使用されます。|
|`transform`|Optionalコンテンツに適用する必要がある[変換のマッピング](#mapping-transformations)。


### <a name="example-of-the-parquet-mapping"></a>Parquet マッピングの例

```json
[
  { "column" : "rownumber",   "Properties":{"Path":"$.rownumber"}}, 
  { "column" : "xdouble",     "Properties":{"Path":"$.xdouble"}}, 
  { "column" : "xbool",       "Properties":{"Path":"$.xbool"}}, 
  { "column" : "xint64",      "Properties":{"Path":"$.xint64"}}, 
  { "column" : "xdate",       "Properties":{"Path":"$.xdate"}}, 
  { "column" : "xtext",       "Properties":{"Path":"$.xtext"}}, 
  { "column" : "location",    "Properties":{"transform":"SourceLocation"}}, 
  { "column" : "lineNumber",  "Properties":{"transform":"SourceLineNumber"}}, 
  { "column" : "full_record", "Properties":{"Path":"$"}}
]
```      

> [!NOTE]
> 上記のマッピングを`.ingest`制御コマンドの一部として指定すると、JSON 文字列としてシリアル化されます。

* 上記のマッピングが[事前に作成](create-ingestion-mapping-command.md)されている場合、 `.ingest`コントロールコマンドで参照できます。

```kusto
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="parquet", 
        ingestionMappingReference = "Mapping1"
    )
```

* 上記のマッピングを`.ingest`制御コマンドの一部として指定すると、JSON 文字列としてシリアル化されます。

```kusto
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "parquet", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"Properties\":{\"Path\":\"$.rownumber\"}},"
        "{\"column\":\"rowguid\",  \"Properties\":{\"Path\":\"$.rowguid\"}}"
      "]"
  )
```

## <a name="orc-mapping"></a>Orc のマッピング

ソースファイルが Orc 形式の場合、ファイルの内容は Kusto テーブルにマップされます。 マップされているすべての列に有効なデータ型が指定されていない限り、テーブルは Kusto データベースに存在する必要があります。 Orc マッピングでマップされている列は、すべての既存でない列にデータ型が指定されていない限り、Kusto テーブルに存在する必要があります。

リスト内の各要素には、特定の列のマッピングが記述され、次のプロパティが含まれる場合があります。

|プロパティ|説明|
|----|--|
|`path`|がで`$`始まる場合: Orc ドキュメント内の列のコンテンツになるフィールドへの json パス (ドキュメント全体が`$`であることを示す json パス)。 値が次の値で`$`始まらない場合: 定数値が使用されます。|
|`transform`|Optionalコンテンツに適用する必要がある[変換のマッピング](#mapping-transformations)。

### <a name="example-of-orc-mapping"></a>Orc mapping の例

```json
[
  { "column" : "rownumber",   "Properties":{"Path":"$.rownumber"}}, 
  { "column" : "xdouble",     "Properties":{"Path":"$.xdouble"}}, 
  { "column" : "xbool",       "Properties":{"Path":"$.xbool"}}, 
  { "column" : "xint64",      "Properties":{"Path":"$.xint64"}}, 
  { "column" : "xdate",       "Properties":{"Path":"$.xdate"}}, 
  { "column" : "xtext",       "Properties":{"Path":"$.xtext"}}, 
  { "column" : "location",    "Properties":{"transform":"SourceLocation"}}, 
  { "column" : "lineNumber",  "Properties":{"transform":"SourceLineNumber"}}, 
  { "column" : "full_record", "Properties":{"Path":"$"}}
]
```      

> [!NOTE]
> 上記のマッピングを`.ingest`制御コマンドの一部として指定すると、JSON 文字列としてシリアル化されます。

```kusto
.ingest into Table123 (@"source1", @"source2") 
  with 
  (
      format = "orc", 
      ingestionMapping = 
      "["
        "{\"column\":\"rownumber\",\"Properties\":{\"Path\":\"$.rownumber\"}},"
        "{\"column\":\"rowguid\",  \"Properties\":{\"Path\":\"$.rowguid\"}}"
      "]"
  )
```

## <a name="mapping-transformations"></a>マッピング変換

一部のデータ形式マッピング (Parquet、JSON、Avro) では、簡単で便利な取り込み時間の変換がサポートされています。 取り込み時により複雑な処理を必要とするシナリオでは、[更新ポリシー](update-policy.md)を使用します。これにより、kql 式を使用して簡易処理を定義できます。

|パスに依存する変換|説明|条件|
|--|--|--|
|`PropertyBagArrayToDictionary`|プロパティの JSON 配列 ({events: [{"n1": "v1"}、{"n2": "v2"}]}) をディクショナリに変換し、有効な JSON ドキュメント (たとえば、{"n1": "v1", "n2": "v2"}) にシリアル化します。|は、が使用さ`path`れている場合にのみ適用できます。|
|`GetPathElement(index)`|指定されたインデックスに従って、指定されたパスから要素を抽出します (たとえば、Path: $. a. b. c、GetPathElement (0) = = "c"、GetPathElement (-1) = = "b"、型文字列など)。|は、が使用さ`path`れている場合にのみ適用できます。|
|`SourceLocation`|データを提供したストレージアーティファクトの名前です。たとえば、「string」と入力します (たとえば、blob の "BaseUri" フィールド)。|
|`SourceLineNumber`|そのストレージアーティファクトを基準としたオフセット。 long (' 1 ' から始まり、新しいレコードごとに増分) を入力します。|
|`DateTimeFromUnixSeconds`|Unix 時間 (1970-01-01 以降の秒) を表す数値を UTC の datetime 文字列に変換します|
|`DateTimeFromUnixMilliseconds`|Unix 時間 (ミリ秒1970-01-01 からのミリ秒) を表す数値を UTC の datetime 文字列に変換します|
|`DateTimeFromUnixMicroseconds`|Unix 時間 (マイクロ1970-01-01 秒) を表す数値を UTC の datetime 文字列に変換します|
|`DateTimeFromUnixNanoseconds`|Unix 時間 (ナノ秒1970-01-01 からのナノ秒) を表す数値を UTC の datetime 文字列に変換します|
