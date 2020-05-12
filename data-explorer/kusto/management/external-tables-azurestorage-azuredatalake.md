---
title: Azure Storage または Azure Data Lake の外部テーブル-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの外部テーブル管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: db99d1d46c321bff0f5d7b370766900ea7d1d5a0
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227725"
---
# <a name="external-tables-in-azure-storage-or-azure-data-lake"></a>Azure Storage または Azure Data Lake 内の外部テーブル

次のコマンドでは、外部テーブルを作成する方法について説明します。 このテーブルは、Azure Blob Storage、Azure Data Lake Store Gen1、または Azure Data Lake Store Gen2 に配置できます。 
[ストレージ接続文字列](../api/connection-strings/storage.md)は、これらの各オプションの接続文字列の作成について説明します。 

## <a name="create-or-alter-external-table"></a>。外部テーブルを作成または変更します。

**構文**

( `.create`  |  `.alter` ) `external` `table` *TableName* (*スキーマ*)  
`kind` `=` (`blob` | `adl`)  
[ `partition` `by` *パーティション*[ `,` ...]]  
`dataformat` `=` *Format*  
`(`  
*StorageConnectionString* [ `,` ...]  
`)`  
[ `with` `(` [ `docstring` `=` *ドキュメント*] [ `,` `folder` `=` *FolderName*]、 *property_name* `=` *値* `,` ... `)` ]

コマンドが実行されるデータベース内の新しい外部テーブルを作成または変更します。

**パラメーター**

* *TableName* -外部テーブル名。 [エンティティ名](../query/schema-entities/entity-names.md)の規則に従う必要があります。 外部テーブルは、同じデータベース内の通常のテーブルと同じ名前にすることはできません。
* *スキーマ*-外部データスキーマの形式: `ColumnName:ColumnType[, ColumnName:ColumnType ...]` 。 外部データスキーマが不明な場合は、 [infer_storage_schema](../query/inferstorageschemaplugin.md)プラグインを使用します。これにより、外部のファイルの内容に基づいてスキーマを推測できます。
* *Partition* -1 つまたは複数のパーティション定義 (省略可能)。 後述のパーティション構文を参照してください。
* *Format* -データ形式。 すべての[インジェスト形式](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)は、クエリに対してサポートされています。 [エクスポートシナリオ](data-export/export-data-to-an-external-table.md)での外部テーブルの使用は、、、 `CSV` `TSV` `JSON` 、の各形式に制限され `Parquet` ます。
* *StorageConnectionString* -資格情報を含む Azure Blob Storage Blob コンテナーまたは Azure Data Lake Store ファイルシステム (仮想ディレクトリまたはフォルダー) への1つまたは複数のパス。 詳細については、「[ストレージ接続文字列](../api/connection-strings/storage.md)」を参照してください。 大量のデータを外部テーブルに[エクスポート](data-export/export-data-to-an-external-table.md)するときにストレージの調整を回避するには、ストレージアカウントを1つ以上指定します。 エクスポートすると、提供されたすべてのアカウント間の書き込みが分散されます。 

**パーティションの構文**

[ `format_datetime =` *DateTimePartitionFormat*] `bin(`タイム*スタンプ columnname*、 *partitionbytimespan*`)`  
|   
[*Stringformatprefix*]*Stringcolumnname* [*stringformatsuffix*])

**パーティションパラメーター**

* *DateTimePartitionFormat* -出力パス内の必要なディレクトリ構造の形式 (省略可能)。 パーティション分割が定義されていて、format が指定されていない場合、既定値は "yyyy/MM/dd/HH/mm" になります。 この形式は PartitionByTimeSpan に基づいています。 たとえば、1d でパーティション分割する場合、structure は "yyyy/MM/dd" になります。 1 h でパーティション分割すると、structure は "yyyy/MM/dd/HH" になります。
* タイム*スタンプ columnname* -テーブルがパーティション分割されている Datetime 列。 外部テーブルにエクスポートする場合は、外部テーブルのスキーマ定義とエクスポートクエリの出力に Timestamp 列が存在している必要があります。
* *Partitionbytimespan* -パーティション分割する timespan リテラル。
* *Stringformatprefix* -テーブル値 (省略可能) の前に連結された成果物パスの一部となる定数文字列リテラル。
* *Stringformatsuffix* -テーブル値 (省略可能) の後に連結された成果物パスの一部となる定数文字列リテラル。
* *Stringcolumnname* -テーブルがパーティション分割される文字列列。 文字列型の列は、外部テーブルのスキーマ定義に存在する必要があります。

**省略可能なプロパティ**:

| プロパティ         | Type     | 説明       |
|------------------|----------|-------------------------------------------------------------------------------------|
| `folder`         | `string` | テーブルのフォルダー                                                                     |
| `docString`      | `string` | テーブルをドキュメント化する文字列                                                       |
| `compressed`     | `bool`   | 設定した場合、blob がファイルとして圧縮されるかどうかを示します。 `.gz`                  |
| `includeHeaders` | `string` | CSV または TSV blob の場合、blob にヘッダーが含まれているかどうかを示します                     |
| `namePrefix`     | `string` | 設定すると、blob のプレフィックスを示します。 書き込み操作では、すべての blob がこのプレフィックスを使用して書き込まれます。 読み取り操作では、このプレフィックスを持つ blob だけが読み取られます。 |
| `fileExtension`  | `string` | 設定すると、blob のファイル拡張子が示されます。 書き込み時には、blob 名はこのサフィックスで終了します。 読み取り時には、このファイル拡張子を持つ blob だけが読み取られます。           |
| `encoding`       | `string` | テキストのエンコード方法を示します。 `UTF8NoBOM` (既定値) または `UTF8BOM` 。             |

クエリでの外部テーブルパラメーターの詳細については、「[アーティファクトフィルター処理ロジック](#artifact-filtering-logic)」を参照してください。

> [!NOTE]
> * テーブルが存在する場合、 `.create` コマンドは失敗し、エラーが表示されます。 `.alter`既存のテーブルを変更するには、を使用します。 
> * 外部 blob テーブルのスキーマ、形式、またはパーティション定義の変更はサポートされていません。 

に対する[データベースユーザー権限](../management/access-control/role-based-authorization.md) `.create` と、の[Table admin 権限](../management/access-control/role-based-authorization.md)が必要です `.alter` 。 
 
**例** 

パーティション分割されていない外部テーブルです。 すべての成果物は、定義されているコンテナーの直下にあると想定されます。

```kusto
.create external table ExternalBlob (x:long, s:string) 
kind=blob
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"
)  
```

DateTime でパーティション分割された外部テーブル。 アーティファクトは、定義されているパスの下の "yyyy/MM/dd" 形式のディレクトリにあります。

```kusto
.create external table ExternalAdlGen2 (Timestamp:datetime, x:long, s:string) 
kind=adl
partition by bin(Timestamp, 1d)
dataformat=csv
( 
   h@'abfss://filesystem@storageaccount.dfs.core.windows.net/path;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"
)  
```

DateTime でパーティション分割され、"year = yyyy/month = MM/day = dd" というディレクトリ形式の外部テーブル。

```kusto
.create external table ExternalPartitionedBlob (Timestamp:datetime, x:long, s:string) 
kind=blob
partition by format_datetime="'year='yyyy/'month='MM/'day='dd" bin(Timestamp, 1d)
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"
)
```

月単位のデータパーティションと "yyyy/MM" のディレクトリ形式を持つ外部テーブル:

```kusto
.create external table ExternalPartitionedBlob (Timestamp:datetime, x:long, s:string) 
kind=blob
partition by format_datetime="yyyy/MM" bin(Timestamp, 1d)
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"
)
```

2つのパーティションを持つ外部テーブルです。 ディレクトリ構造は、両方のパーティションを連結したものです。書式設定された様の後に既定の dateTime 形式が適用されます。 たとえば、"様: softworks/2011/11/11" のようになります。

```kusto
.create external table ExternalMultiplePartitions (Timestamp:datetime, CustomerName:string) 
kind=blob
partition by 
   "CustomerName="CustomerName,
   bin(Timestamp, 1d)
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"   
)
```

**出力**

|TableName|TableType|Folder|DocString|Properties|ConnectionStrings|メジャー グループ|
|---|---|---|---|---|---|---|
|External多重パーティション|Blob|ExternalTables|Docs|{"Format": "Csv"、"圧縮": false、"CompressionType": null、"FileExtension": "csv"、"IncludeHeaders": "None"、"Encoding": null、"NamePrefix": null}|["https://storageaccount.blob.core.windows.net/container1;*******"]}|[{"StringFormat": "様名 = {0} ", "ColumnName": "様"、"ColumnName": "様"、"序数": 0}、PartitionBy ":" 1.00:00:00 "、" ColumnName ":" Timestamp "、" Ordinal ": 1}]|

### <a name="artifact-filtering-logic"></a>アーティファクトのフィルター処理ロジック

外部テーブルに対してクエリを実行する場合、クエリエンジンは、無関係の外部ストレージアーティファクト (blob) を除外することによってパフォーマンスを向上させます。 Blob を反復処理し、blob を処理する必要があるかどうかを判断するプロセスを次に示します。

1. Blob が検出される場所を表す URI パターンを作成します。 最初、URI パターンは、外部テーブル定義の一部として指定された接続文字列に相当します。 パーティションが定義されている場合は、URI パターンに追加されます。
たとえば、接続文字列が `https://storageaccount.blob.core.windows.net/container1` で、datetime パーティションが定義されている場合、対応する URI パターンは次のようになります。 `partition by format_datetime="yyyy-MM-dd" bin(Timestamp, 1d)` `https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd` このパターンに一致する場所にある blob が検索されます。
追加の文字列パーティションが `"CustomerId" customerId` 定義されている場合、対応する URI パターンはに `https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd/CustomerId=*` なります。

1. 作成した URI パターンに含まれるすべての*ダイレクト*blob について、次のことを確認します。

   * パーティション値は、クエリで使用される述語と一致します。
   * `NamePrefix`このようなプロパティが定義されている場合、Blob 名はで始まります。
   * `FileExtension`このようなプロパティが定義されている場合、Blob 名はで終了します。

すべての条件が満たされると、blob がフェッチされ、クエリエンジンによって処理されます。

### <a name="spark-virtual-columns-support"></a>Spark 仮想列のサポート

データが Spark からエクスポートされると、パーティション列 (データフレーム writer のメソッドで指定 `partitionBy` ) はデータファイルに書き込まれません。 このプロセスでは、"フォルダー" という名前のデータが既に存在するため、データの重複が回避されます。 たとえば、 `column1=<value>/column2=<value>/` 、、および Spark は、読み取り時にそれを認識できます。 ただし、Kusto では、パーティション列がデータ自体に存在する必要があります。 Kusto での仮想列のサポートは計画されています。 それまでは、次の回避策を使用してください。 Spark からデータをエクスポートする場合は、データフレームを書き込む前に、データがパーティション分割されているすべての列のコピーを作成します。

```kusto
df.withColumn("_a", $"a").withColumn("_b", $"b").write.partitionBy("_a", "_b").parquet("...")
```

Kusto に外部テーブルを定義する場合は、次の例のようにパーティション列を指定します。

```kusto
.create external table ExternalSparkTable(a:string, b:datetime) 
kind=blob
partition by 
   "_a="a,
   format_datetime="'_b='yyyyMMdd" bin(b, 1d)
dataformat=parquet
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
```

## <a name="show-external-table-artifacts"></a>。外部テーブルの成果物を表示する

* 指定された外部テーブルに対してクエリを実行するときに処理されるすべてのアイテムの一覧を返します。
* [データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

**構文 :** 

`.show``external` `table` *TableName*`artifacts`

**出力**

| 出力パラメーター | Type   | 説明                       |
|------------------|--------|-----------------------------------|
| URI              | string | 外部ストレージの成果物の URI |

**例:**

```kusto
.show external table T artifacts
```

**出力:**

| URI                                                                     |
|-------------------------------------------------------------------------|
| `https://storageaccount.blob.core.windows.net/container1/folder/file.csv` |

## <a name="create-external-table-mapping"></a>。外部テーブルマッピングを作成します。

`.create``external` `table` *Externaltablename* `json` `mapping` *MappingName* *MappingInJsonFormat*

新しいマッピングを作成します。 詳細については、「[データのマッピング](./mappings.md#json-mapping)」を参照してください。

**例** 
 
```kusto
.create external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**出力例**

| 名前     | 種類 | マッピング                                                           |
|----------|------|-------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "rownumber", "ColumnType": "int", "Properties": {"Path": "$. rownumber"}}, {"ColumnName": "rowguid", "ColumnType": "", "Properties": {"Path": "$. rowguid"}}] |

## <a name="alter-external-table-mapping"></a>。外部テーブルマッピングを変更します。

`.alter``external` `table` *Externaltablename* `json` `mapping` *MappingName* *MappingInJsonFormat*

既存のマッピングを変更します。 
 
**例** 
 
```kusto
.alter external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**出力例**

| 名前     | 種類 | マッピング                                                                |
|----------|------|------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "rownumber", "ColumnType": "", "Properties": {"Path": "$. rownumber"}}, {"ColumnName": "rowguid", "ColumnType": "", "Properties": {"Path": "$. rowguid"}}] |

## <a name="show-external-table-mappings"></a>。外部テーブルマッピングを表示します。

`.show``external` `table` *Externaltablename* `json` `mapping` *MappingName* 

`.show``external` `table` *ExternalTableName* Externaltablename `json``mappings`

マッピング (すべてまたは名前で指定したもの) を表示します。
 
**例** 
 
```kusto
.show external table MyExternalTable JSON mapping "Mapping1" 

.show external table MyExternalTable JSON mappings 
```

**出力例**

| 名前     | 種類 | マッピング                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "rownumber", "ColumnType": "", "Properties": {"Path": "$. rownumber"}}, {"ColumnName": "rowguid", "ColumnType": "", "Properties": {"Path": "$. rowguid"}}] |

## <a name="drop-external-table-mapping"></a>。外部テーブルマッピングを削除します。

`.drop``external` `table` *Externaltablename* `json` `mapping` *MappingName* 

データベースからマッピングを削除します。
 
**例** 
 
```kusto
.drop external table MyExternalTable JSON mapping "Mapping1" 
```
