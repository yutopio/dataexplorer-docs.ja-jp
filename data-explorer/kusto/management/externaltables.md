---
title: 外部テーブル管理-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの外部テーブル管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: c52f0649531678e31310f5a1f4bfb97f99f15857
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741938"
---
# <a name="external-table-management"></a>外部テーブル管理

外部テーブルの概要については、「[外部テーブル](../query/schema-entities/externaltables.md)」を参照してください。 

## <a name="common-external-tables-control-commands"></a>一般的な外部テーブルコントロールコマンド

次のコマンドは _、任意の外部テーブル_(任意の型) に関連しています。

### <a name="show-external-tables"></a>。外部テーブルを表示します。

* データベース内のすべての外部テーブル (または特定の外部テーブル) を返します。
* [データベースモニターのアクセス許可](../management/access-control/role-based-authorization.md)が必要です。

**構文 :** 

`.show` `external` `tables`

`.show``external` *TableName* TableName `table`

**出力**

| 出力パラメーター | Type   | 説明                                                         |
|------------------|--------|---------------------------------------------------------------------|
| TableName        | string | 外部テーブルの名前                                             |
| TableType        | string | 外部テーブルの種類                                              |
| Folder           | string | テーブルのフォルダー                                                     |
| DocString        | string | テーブルをドキュメント化する文字列                                       |
| Properties       | string | テーブルの JSON でシリアル化されたプロパティ (テーブルの型に固有) |


**例:**

```kusto
.show external tables
.show external table T
```

| TableName | TableType | Folder         | DocString | Properties |
|-----------|-----------|----------------|-----------|------------|
| T         | BLOB      | ExternalTables | Docs      | {}         |


### <a name="show-external-table-schema"></a>。外部テーブルスキーマを表示します

* JSON または CSL として、外部テーブルのスキーマを返します。 
* [データベースモニターのアクセス許可](../management/access-control/role-based-authorization.md)が必要です。

**構文 :** 

`.show``external` `schema` *TableName* (`json`) `table` `as`  | `csl`

`.show``external` *TableName* TableName `table``cslschema`

**出力**

| 出力パラメーター | Type   | 説明                        |
|------------------|--------|------------------------------------|
| TableName        | string | 外部テーブルの名前            |
| スキーマ           | string | JSON 形式のテーブルスキーマ |
| DatabaseName     | string | テーブルのデータベース名             |
| Folder           | string | テーブルのフォルダー                    |
| DocString        | string | テーブルをドキュメント化する文字列      |

**例:**

```kusto
.show external table T schema as JSON
```

```kusto
.show external table T schema as CSL
.show external table T cslschema
```

**出力:**

*json*

| TableName | スキーマ    | DatabaseName | Folder         | DocString |
|-----------|----------------------------------|--------------|----------------|-----------|
| T         | {"Name": "ExternalBlob",<br>"Folder": "ExternalTables",<br>"DocString": "Docs",<br>"OrderedColumns": [{"Name": "x", "Type": "system.string", "DocString", "Type": "system.string", "CslType": "string", "DocString": ""}]} を指定すると、"{" Name ":" x "," Type ":" system.object "," " | DB           | ExternalTables | Docs      |


*csl*

| TableName | スキーマ          | DatabaseName | Folder         | DocString |
|-----------|-----------------|--------------|----------------|-----------|
| T         | x:long、-string | DB           | ExternalTables | Docs      |

### <a name="drop-external-table"></a>。外部テーブルを削除します。

* 外部テーブルを削除します。 
* この操作後、外部テーブルの定義を復元できません
* [Database admin 権限](../management/access-control/role-based-authorization.md)が必要です。

**構文 :**  

`.drop``external` *TableName* TableName `table`

**出力**

削除されたテーブルのプロパティを返します。 「[外部テーブルを表示する」を](#show-external-tables)参照してください。

**例:**

```kusto
.drop external table ExternalBlob
```

| TableName | TableType | Folder         | DocString | スキーマ       | Properties |
|-----------|-----------|----------------|-----------|-----------------------------------------------------|------------|
| T         | BLOB      | ExternalTables | Docs      | [{"Name": "x", "CslType": "long"},<br> {"Name": "s", "CslType": "string"}] | {}         |

## <a name="external-tables-in-azure-storage-or-azure-data-lake"></a>Azure Storage または Azure Data Lake 内の外部テーブル

次のコマンドでは、外部テーブルを作成する方法について説明します。 このテーブルは、Azure Blob Storage、Azure Data Lake Store Gen1、または Azure Data Lake Store Gen2 に配置できます。 
[ストレージ接続文字列](../api/connection-strings/storage.md)は、これらの各オプションの接続文字列の作成について説明します。 

### <a name="create-or-alter-external-table"></a>。外部テーブルを作成または変更します。

**構文**

`.create` | (`.alter`) `external` *TableName (* *スキーマ*) `table`  
`kind` `=` (`blob` | `adl`)  
[`partition` `by` *パーティション*[`,` ...]]  
`dataformat``=` *形式*  
`(`  
*StorageConnectionString* [`,` ...]  
`)`  
[`with` `(`[`docstring` `=` *FolderName* `=` *value* *Documentation* `folder` `,` *property_name*ドキュメント] [`,` FolderName]、property_name 値... `=``)`]

コマンドが実行されるデータベース内の新しい外部テーブルを作成または変更します。

**Parameters**

* *TableName* -外部テーブル名。 [エンティティ名](../query/schema-entities/entity-names.md)の規則に従う必要があります。 外部テーブルは、同じデータベース内の通常のテーブルと同じ名前にすることはできません。
* *スキーマ*-外部データスキーマの形式: `ColumnName:ColumnType[, ColumnName:ColumnType ...]`。 外部データスキーマが不明な場合は、 [infer_storage_schema](../query/inferstorageschemaplugin.md)プラグインを使用します。これにより、外部のファイルの内容に基づいてスキーマを推測できます。
* *Partition* -1 つまたは複数のパーティション定義 (省略可能)。 後述のパーティション構文を参照してください。
* *Format* -データ形式。 すべての[インジェスト形式](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)は、クエリに対してサポートされています。 [エクスポートシナリオ](data-export/export-data-to-an-external-table.md)での外部テーブルの`CSV`使用は、 `TSV`、、 `JSON`、 `Parquet`の各形式に制限されます。
* *StorageConnectionString* -資格情報を含む Azure Blob Storage Blob コンテナーまたは Azure Data Lake Store ファイルシステム (仮想ディレクトリまたはフォルダー) への1つまたは複数のパス。 詳細については、「[ストレージ接続文字列](../api/connection-strings/storage.md)」を参照してください。 大量のデータを外部テーブルに[エクスポート](data-export/export-data-to-an-external-table.md)する場合は、ストレージの調整を回避するために、1つ以上のストレージアカウントを提供することを強くお勧めします。 エクスポートすると、提供されたすべてのアカウント間の書き込みが分散されます。 

**パーティションの構文**

[`format_datetime =` *DateTimePartitionFormat*]`bin(`タイム*スタンプ columnname*、 *partitionbytimespan*`)`  
|   
[*Stringformatprefix*]*Stringcolumnname* [*stringformatsuffix*])

**パーティションパラメーター**

* *DateTimePartitionFormat* -出力パス内の必要なディレクトリ構造の形式 (省略可能)。 パーティション分割が定義されていて、format が指定されていない場合、PartitionByTimeSpan に基づく既定値は "yyyy/MM/dd/HH/mm" です。 たとえば、1d でパーティション分割する場合、structure は "yyyy/MM/dd" になります。 1h でパーティション分割する場合、構造は "yyyy/MM/dd/HH" になります。
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
| `compressed`     | `bool`   | 設定した場合、blob がファイルとし`.gz`て圧縮されるかどうかを示します。                  |
| `includeHeaders` | `string` | CSV または TSV blob の場合、blob にヘッダーが含まれているかどうかを示します                     |
| `namePrefix`     | `string` | 設定すると、blob のプレフィックスを示します。 書き込み操作では、すべての blob がこのプレフィックスを使用して書き込まれます。 読み取り操作では、このプレフィックスを持つ blob だけが読み取られます。 |
| `fileExtension`  | `string` | 設定すると、blob のファイル拡張子が示されます。 書き込み時には、blob 名はこのサフィックスで終了します。 読み取り時には、このファイル拡張子を持つ blob だけが読み取られます。           |
| `encoding`       | `string` | テキストのエンコード方法を示します`UTF8NoBOM` 。 (既定値`UTF8BOM`) または。             |

クエリでの外部テーブルパラメーターの詳細については、「[アーティファクトフィルター処理ロジック](#artifact-filtering-logic)」を参照してください。

> [!NOTE]
> * テーブルが存在する場合`.create` 、コマンドは失敗し、エラーが表示されます。 既存`.alter`のテーブルを変更するには、を使用します。 
> * 外部 blob テーブルのスキーマ、形式、またはパーティション定義の変更はサポートされていません。 

に対する[データベースユーザー権限](../management/access-control/role-based-authorization.md) `.create`と、の`.alter` [Table admin 権限](../management/access-control/role-based-authorization.md)が必要です。 
 
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

DateTime でパーティション分割された外部テーブル。 アーティファクトは、定義されているパスの下にある "yyyy/MM/dd" 形式のディレクトリに存在します。

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

2つのパーティションを持つ外部テーブルです。 ディレクトリ構造は、両方のパーティションを連結したものです。書式設定された様の後に既定の dateTime 形式が適用されます。 たとえば、"様の場合は、softworks/2011/11/11" のようになります。

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
|External多重パーティション|BLOB|ExternalTables|Docs|{"Format": "Csv"、"圧縮": false、"CompressionType": null、"FileExtension": "csv"、"IncludeHeaders": "None"、"Encoding": null、"NamePrefix": null}|["https://storageaccount.blob.core.windows.net/container1;*******"]}|[{"StringFormat": "様名{0}=", "ColumnName": "様"、"ColumnName": "様"、"序数": 0}、partitionby ":" 1.00:00:00 "、" ColumnName ":" Timestamp "、" Ordinal ": 1}]|

#### <a name="artifact-filtering-logic"></a>アーティファクトのフィルター処理ロジック

外部テーブルに対してクエリを実行する場合、クエリエンジンは、クエリのパフォーマンスを向上させるために、無関係の外部ストレージアーティファクト (blob) を除外します。 Blob を反復処理し、blob を処理する必要があるかどうかを判断するプロセスを次に示します。

1. Blob が検出される場所を表す URI パターンを作成します。 最初、URI パターンは、外部テーブル定義の一部として指定された接続文字列に相当します。 パーティションが定義されている場合は、URI パターンに追加されます。
たとえば、接続文字列が`https://storageaccount.blob.core.windows.net/container1` `partition by format_datetime="yyyy-MM-dd" bin(Timestamp, 1d)`で、datetime パーティションが定義されている場合、対応する URI パターンは次`https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd`のようになります。このパターンに一致する場所にある blob が検索されます。
追加の文字列パーティション`"CustomerId" customerId`が定義されている場合、対応する URI パターン`https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd/CustomerId=*`は、などです。

2. 作成した URI パターンに含まれるすべての*ダイレクト*blob について、次のことを確認します。

 * パーティション値は、クエリで使用される述語と一致します。
 * このようなプロパティ`NamePrefix`が定義されている場合、Blob 名はで始まります。
 * このようなプロパティ`FileExtension`が定義されている場合、Blob 名はで終了します。

すべての条件が満たされると、blob がフェッチされ、クエリエンジンによって処理されます。

#### <a name="spark-virtual-columns-support"></a>Spark 仮想列のサポート

データが Spark からエクスポートされると、パーティション列 (データフレーム writer の`partitionBy`メソッドで指定) はデータファイルに書き込まれません。 これは、データが "フォルダー" 名に既に存在するため (たとえば、)、データ`column1=<value>/column2=<value>/`が重複しないようにするために行われます。これは、データが読み取り時に認識されるためです。 ただし、Kusto では、パーティション列がデータ自体に存在する必要があります。 Kusto での仮想列のサポートは計画されています。 それまでは、次の回避策を使用してください。 Spark からデータをエクスポートする場合は、データフレームを書き込む前に、データがパーティション分割されているすべての列のコピーを作成します。

```kusto
df.withColumn("_a", $"a").withColumn("_b", $"b").write.partitionBy("_a", "_b").parquet("...")
```

Kusto 外部テーブルを定義して、次の例のようにパーティション列を指定する場合:

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

### <a name="show-external-table-artifacts"></a>。外部テーブルの成果物を表示する

* 指定された外部テーブルに対してクエリを実行するときに処理されるすべてのアイテムの一覧を返します。
* [データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

**構文 :** 

`.show``external` *TableName* TableName `table``artifacts`

**出力**

| 出力パラメーター | Type   | 説明                       |
|------------------|--------|-----------------------------------|
| Uri              | string | 外部ストレージの成果物の URI |

**例:**

```kusto
.show external table T artifacts
```

**出力:**

| Uri                                                                     |
|-------------------------------------------------------------------------|
| `https://storageaccount.blob.core.windows.net/container1/folder/file.csv` |

### <a name="create-external-table-mapping"></a>。外部テーブルマッピングを作成します。

`.create``external` `mapping` *MappingName* *MappingInJsonFormat* Externaltablename `json` MappingName MappingInJsonFormat *ExternalTableName* `table`

新しいマッピングを作成します。 詳細については、「[データのマッピング](./mappings.md#json-mapping)」を参照してください。

**例** 
 
```kusto
.create external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**出力例**

| 名前     | 種類 | マッピング                                                           |
|----------|------|-------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "rownumber", "ColumnType": "int", "Properties": {"Path": "$. rownumber"}}, {"ColumnName": "rowguid", "ColumnType": "", "Properties": {"Path": "$. rowguid"}}] |

### <a name="alter-external-table-mapping"></a>。外部テーブルマッピングを変更します。

`.alter``external` `mapping` *MappingName* *MappingInJsonFormat* Externaltablename `json` MappingName MappingInJsonFormat *ExternalTableName* `table`

既存のマッピングを変更します。 
 
**例** 
 
```kusto
.alter external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**出力例**

| 名前     | 種類 | マッピング                                                                |
|----------|------|------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "rownumber", "ColumnType": "", "Properties": {"Path": "$. rownumber"}}, {"ColumnName": "rowguid", "ColumnType": "", "Properties": {"Path": "$. rowguid"}}] |

### <a name="show-external-table-mappings"></a>。外部テーブルマッピングを表示します。

`.show``external` `mapping` *MappingName* *ExternalTableName* Externaltablename `json` MappingName `table` 

`.show``external` `json` *ExternalTableName* Externaltablename `table``mappings`

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

### <a name="drop-external-table-mapping"></a>。外部テーブルマッピングを削除します。

`.drop``external` `mapping` *MappingName* *ExternalTableName* Externaltablename `json` MappingName `table` 

データベースからマッピングを削除します。
 
**例** 
 
```kusto
.drop external table MyExternalTable JSON mapping "Mapping1" 
```

## <a name="external-sql-table"></a>外部 SQL テーブル

### <a name="create-or-alter-external-sql-table"></a>。外部 sql テーブルを作成または変更します。

コマンドが実行されるデータベース内の外部 SQL テーブルを作成または変更します。  

**構文**

`.create` | (`.alter`) `external` TableName ([columnName: columnType],...) *TableName* `table`  
`kind` `=` `sql`  
`table``=` *Sqltablename*  
`(`*SqlServerConnectionString*`)`  
[`with` `(`[`docstring` `=` *FolderName* `=` *value* *Documentation* `folder` `,` *property_name*ドキュメント] [`,` FolderName]、property_name 値... `=``)`]


**パラメータ:**

* *TableName* -外部テーブル名。 [エンティティ名](../query/schema-entities/entity-names.md)の規則に従う必要があります。 外部テーブルは、同じデータベース内の通常のテーブルと同じ名前にすることはできません。
* *Sqltablename* -SQL テーブルの名前。
* *SqlServerConnectionString* -SQL server への接続文字列。 以下のいずれかを指定できます。 
    * **Aad 統合認証**(`Authentication="Active Directory Integrated"`): ユーザーまたはアプリケーションが aad 経由で kusto に対して認証を行い、同じトークンを使用して SQL Server ネットワークエンドポイントにアクセスします。
    * **ユーザー名/パスワード認証**(`User ID=...; Password=...;`)。 外部テーブルを[連続エクスポート](data-export/continuous-data-export.md)に使用する場合は、この方法を使用して認証を実行する必要があります。 

> [!WARNING]
> 機密情報を含む接続文字列とクエリは、すべての Kusto トレースから除外されるように難読化する必要があります。 詳細については、「[難読化文字列リテラル](../query/scalar-data-types/string.md#obfuscated-string-literals)」を参照してください。

**省略可能なプロパティ**
| プロパティ            | Type            | 説明                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `folder`            | `string`        | テーブルのフォルダー。                  |
| `docString`         | `string`        | テーブルを文書化する文字列。      |
| `firetriggers`      | `true`/`false`  | の`true`場合、SQL テーブルで定義された挿入トリガーを起動するように対象システムに指示します。 既定では、 `false`です。 (詳細については、「 [BULK INSERT](https://msdn.microsoft.com/library/ms188365.aspx)と[SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx))」を参照してください。 |
| `createifnotexists` | `true`/ `false` | の`true`場合、対象の SQL テーブルがまだ存在しない場合は作成されます。この`primarykey`場合、プロパティを指定して、主キーである結果列を示す必要があります。 既定では、 `false`です。  |
| `primarykey`        | `string`        | が`createifnotexists` `true`の場合、このコマンドで SQL テーブルの主キーとして使用される、結果の列の名前を示します。                  |

> [!NOTE]
> * テーブルが存在する場合、 `.create`コマンドは失敗し、エラーが表示されます。 既存`.alter`のテーブルを変更するには、を使用します。 
> * 外部 SQL テーブルのスキーマまたは形式の変更はサポートされていません。 

に対する[データベースユーザー権限](../management/access-control/role-based-authorization.md) `.create`と、の`.alter` [table admin 権限](../management/access-control/role-based-authorization.md)が必要です。 
 
**例** 

```kusto
.create external table ExternalSql (x:long, s:string) 
kind=sql
table=MySqlTable
( 
   h@'Server=tcp:myserver.database.windows.net,1433;Authentication=Active Directory Integrated;Initial Catalog=mydatabase;'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables", 
   createifnotexists = true,
   primarykey = x,
   firetriggers=true
)  
```

**出力**

| TableName   | TableType | Folder         | DocString | Properties                            |
|-------------|-----------|----------------|-----------|---------------------------------------|
| ExternalSql | Sql       | ExternalTables | Docs      | {<br>  "TargetEntityKind": "sqltable",<br>  "TargetEntityName": "MySqlTable",<br>  "TargetEntityConnectionString": "Server = tcp: database. windows. net, 1433;Authentication = Active Directory Integrated、Initial Catalog = mydatabase; "、<br>  "FireTriggers": true、<br>  "CreateIfNotExists": true、<br>  "PrimaryKey": "x"<br>} |

### <a name="querying-an-external-table-of-type-sql"></a>SQL 型の外部テーブルのクエリ 
他の種類の外部テーブルへの Similarl、外部 SQL テーブルのクエリはサポートされています。 「[外部テーブルのクエリ](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)」を参照してください。 SQL 外部テーブルのクエリ実装では、SQL テーブルから完全な ' SELECT * ' (または関連する列の選択) が実行されますが、クエリの残りの部分は Kusto 側で実行されることに注意してください。 次の外部テーブルクエリについて考えてみます。 

```kusto
external_table('MySqlExternalTable') | count
```

Kusto は、SQL データベースに対して ' SELECT * from TABLE ' クエリを実行し、その後に Kusto 側のカウントを実行します。 このような場合は、外部テーブル関数を使用するのではなく、T-sql で直接記述し (' SELECT COUNT (1) FROM TABLE ')、 [sql_request プラグイン](../query/sqlrequestplugin.md)を使用して実行すると、パフォーマンスが向上します。 同様に、フィルターは SQL クエリにプッシュされません。  
クエリが Kusto 側でさらに実行するためにテーブル全体 (または関連する列) を読み取る必要がある場合は、外部テーブルを使用して SQL テーブルに対してクエリを実行することをお勧めします。 T-sql で SQL クエリを大幅に最適化できる場合は、 [sql_request プラグイン](../query/sqlrequestplugin.md)を使用します。
