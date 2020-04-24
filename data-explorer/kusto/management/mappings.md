---
title: データ マッピング - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのデータ マッピングについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 0d94815eedfd551a09a979c57c68baf125abec40
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520776"
---
# <a name="data-mappings"></a>データ マッピング

データ マッピングは、受信データを Kusto テーブル内の列にマップするために、取り込み中に使用されます。

Kusto は、さまざまな種類のマッピング`row-oriented`(CSV、JSON、AVRO)、`column-oriented`および (寄木細工) の両方をサポートします。

マッピング リストの各要素は、次の 3 つのプロパティから構成されます。

|プロパティ|説明|
|----|--|
|`column`|Kusto テーブルのターゲット列名|
|`datatype`| (オプション)マップされた列が Kusto テーブルに存在しない場合に、その列を作成するデータ型|
|`Properties`|(オプション)各マッピングに固有のプロパティを含むプロパティ バッグ( 以下の各セクションで説明します)。


すべてのマッピングは[事前に作成](create-ingestion-mapping-command.md)でき、パラメーターを使用して`ingestionMappingReference`ingest コマンドから参照できます。

## <a name="csv-mapping"></a>CSV マッピング

ソース ファイルが CSV (または任意の非ミリメートル分離形式) で、そのスキーマが現在の Kusto テーブル スキーマと一致しない場合、CSV マッピングはファイル スキーマから Kusto テーブル スキーマにマッピングされます。 テーブルが Kusto に存在しない場合、このマッピングに従って作成されます。 マッピング内のフィールドがテーブルに存在しない場合は、追加されます。 

CSV マッピングは、すべての区切り文字で区切られた形式(CSV、TSV、PSV、SCSV、および SOHsv)に適用できます。

リストの各要素は、特定の列のマッピングを記述し、次のプロパティを含めることができます。

|プロパティ|説明|
|----|--|
|`ordinal`|CSV の列の順序番号|
|`constantValue`|(オプション)CSV 内の値ではなく、列に使用される定数値|

> [!NOTE]
> `Ordinal`また`ConstantValue`、相互に排他的です。

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
> 上記のマッピングが`.ingest`コントロールコマンドの一部として提供される場合、JSON文字列としてシリアル化されます。

* 上記のマッピングが[事前に作成](create-ingestion-mapping-command.md)されている場合は、`.ingest`コントロールコマンドで参照できます。
```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="csv", 
        ingestionMappingReference = "Mapping1"
    )
```

* 上記のマッピングが`.ingest`コントロールコマンドの一部として提供される場合、JSON文字列としてシリアル化されます。

```
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

**注:** プロパティ バッグを除く次`Properties`のマッピング形式は現在サポートされていますが、今後は使用されなくなる可能性があります。

```
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

ソースファイルが JSON 形式の場合、ファイルの内容は Kusto テーブルにマップされます。 マップされているすべての列に有効なデータ型が指定されていない場合は、テーブルが Kusto データベースに存在する必要があります。 JSON マッピングでマッピングされる列は、存在しないすべての列にデータ型が指定されていない限り、Kusto テーブルに存在する必要があります。

リストの各要素は、特定の列のマッピングを記述し、次のプロパティを含めることができます。 

|プロパティ|説明|
|----|--|
|`path`|で`$`始まる場合 : JSON ドキュメント内の列のコンテンツになるフィールドへの JSON パス (ドキュメント全体を示す JSON`$`パスは です)。 値が : で`$`始まらない場合は、定数値が使用されます。|
|`transform`|(オプション)マップ変換を使用してコンテンツに適用する[必要がある変換](#mapping-transformations)。|

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
> 上記のマッピングが`.ingest`コントロールコマンドの一部として提供される場合、JSON文字列としてシリアル化されます。

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="json", 
        ingestionMappingReference = "Mapping1"
    )
```

**注:** プロパティ バッグを除く次`Properties`のマッピング形式は現在サポートされていますが、今後は使用されなくなる可能性があります。

```
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
    
## <a name="avro-mapping"></a>アブロマッピング

ソースファイルがAvro形式の場合、Avroファイルの内容はKustoテーブルにマッピングされます。 マップされているすべての列に有効なデータ型が指定されていない場合は、テーブルが Kusto データベースに存在する必要があります。 Avro マッピングでマッピングされた列は、存在しないすべての列にデータ型が指定されていない限り、Kusto テーブルに存在する必要があります。

リストの各要素は、特定の列のマッピングを記述し、次のプロパティを含めることができます。 

|プロパティ|説明|
|----|--|
|`Field`|Avro レコードのフィールドの名前。|
|`Path`|使用する`field`代わりに、必要に応じて Avro レコードフィールドの内部部分を取ることができます。 値は、レコードのルートからの JSON パスを示します。 詳細については、以下の「注」を参照してください。 |
|`transform`|(オプション)[サポートされている](#mapping-transformations)変換を使用してコンテンツに適用する必要がある変換 。|

**メモ**
>[!NOTE]
> * `field`一`path`緒に使用することはできません、1つだけが許可されています。 
> * `path`ルート`$`のみを指すことができず、少なくとも 1 つのレベルのパスが必要です。

以下の 2 つの選択肢は等しい:

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
> 上記のマッピングが`.ingest`コントロールコマンドの一部として提供される場合、JSON文字列としてシリアル化されます。

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="avro", 
        ingestionMappingReference = "Mapping1"
    )
```

**注:** プロパティ バッグを除く次`Properties`のマッピング形式は現在サポートされていますが、今後は使用されなくなる可能性があります。

```
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

## <a name="parquet-mapping"></a>寄木細工マッピング

ソース ファイルが Parquet 形式の場合、ファイルの内容は Kusto テーブルにマップされます。 マップされているすべての列に有効なデータ型が指定されていない場合は、テーブルが Kusto データベースに存在する必要があります。 Parquet マッピングでマップされる列は、存在しないすべての列にデータ型が指定されていない限り、Kusto テーブルに存在する必要があります。

リストの各要素は、特定の列のマッピングを記述し、次のプロパティを含めることができます。

|プロパティ|説明|
|----|--|
|`path`|で`$`始まる場合 : Parquet ドキュメント内の列の内容となるフィールドへの JSON パス (ドキュメント全体を示す JSON`$`パスは です)。 値が : で`$`始まらない場合は、定数値が使用されます。|
|`transform`|(オプション)コンテンツに適用する必要がある[マッピング変換](#mapping-transformations)。


### <a name="example-of-the-parquet-mapping"></a>寄木細工マッピングの例

``` json
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
> 上記のマッピングが`.ingest`コントロールコマンドの一部として提供される場合、JSON文字列としてシリアル化されます。

* 上記のマッピングが[事前に作成](create-ingestion-mapping-command.md)されている場合は、`.ingest`コントロールコマンドで参照できます。

```
.ingest into Table123 (@"source1", @"source2")
    with 
    (
        format="parquet", 
        ingestionMappingReference = "Mapping1"
    )
```

* 上記のマッピングが`.ingest`コントロールコマンドの一部として提供される場合、JSON文字列としてシリアル化されます。

```
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

## <a name="orc-mapping"></a>Orc マッピング

ソース ファイルが Orc 形式の場合、ファイルの内容は Kusto テーブルにマップされます。 マップされているすべての列に有効なデータ型が指定されていない場合は、テーブルが Kusto データベースに存在する必要があります。 Orc マッピングでマップされる列は、存在しないすべての列にデータ型が指定されていない限り、Kusto テーブルに存在する必要があります。

リストの各要素は、特定の列のマッピングを記述し、次のプロパティを含めることができます。

|プロパティ|説明|
|----|--|
|`path`|`$`で始まる場合 : ORC ドキュメント内の列の内容となるフィールドへの JSON パス (ドキュメント全体を示す JSON`$`パスは です)。 値が : で`$`始まらない場合は、定数値が使用されます。|
|`transform`|(オプション)コンテンツに適用する必要がある[マッピング変換](#mapping-transformations)。

### <a name="example-of-orc-mapping"></a>Orc マッピングの例

``` json
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
> 上記のマッピングが`.ingest`コントロールコマンドの一部として提供される場合、JSON文字列としてシリアル化されます。

```
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

## <a name="mapping-transformations"></a>変換のマッピング

データ形式マッピング (Parquet、JSON、Avro) の一部は、シンプルで有用なインジェストタイム変換をサポートしています。 インジェスト時にシナリオでより複雑な処理が必要な場合は、KQL 式を使用して軽量な処理を定義できる[更新ポリシー](update-policy.md)を使用します。

|パス依存変換|説明|条件|
|--|--|--|
|`PropertyBagArrayToDictionary`|プロパティの JSON 配列 ({{"n1":"v1"}、{"n2"v2"}}) をディクショナリに変換し、有効な JSON ドキュメントにシリアル化します ({"n1":"v1","n2":"v2"}など)。|使用時にのみ`path`適用可能|
|`GetPathElement(index)`|指定されたインデックスに従って指定されたパスから要素を抽出します (たとえば、パス: $.a.b.c, GetPathElement(0) ="c", GetPathElement(-1) == "b",タイプ文字列|使用時にのみ`path`適用可能|
|`SourceLocation`|データを提供したストレージ成果物の名前、型文字列 (たとえば、BLOB の "BaseUri" フィールド)。|
|`SourceLineNumber`|そのストレージアーティファクトに対するオフセットは、long ('1'から始まり、新しいレコードごとに増分) と入力します。|
|`DateTimeFromUnixSeconds`|UNIX 時間 (1970-01-01 以降の秒) を表す数値を UTC 日時文字列に変換します。|
|`DateTimeFromUnixMilliseconds`|UNIX 時刻 (1970-01-01 以降のミリ秒) を表す数値を UTC 日時文字列に変換します。|
|`DateTimeFromUnixMicroseconds`|UNIX 時刻 (1970-01-01 以降のマイクロ秒) を表す数値を UTC 日時文字列に変換します。|
|`DateTimeFromUnixNanoseconds`|UNIX 時間 (1970-01-01 以降のナノ秒) を表す数値を UTC 日時文字列に変換します。|