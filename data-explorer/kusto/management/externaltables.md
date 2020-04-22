---
title: 外部テーブル管理 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの外部テーブル管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 680a4e25d6b478fe171aa3296de81c0106417877
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744716"
---
# <a name="external-table-management"></a>外部テーブル管理

[外部テーブル](../query/schema-entities/externaltables.md)の概要については、外部テーブルを参照してください。 

## <a name="common-external-tables-control-commands"></a>共通外部テーブル制御コマンド

次のコマンドは、_任意_の種類の外部テーブルに関連しています。

### <a name="show-external-tables"></a>.show 外部テーブル

* データベース内のすべての外部テーブル (または特定の外部テーブル) を返します。
* [データベース モニタのアクセス許可](../management/access-control/role-based-authorization.md)が必要です。

**構文 :** 

`.show` `external` `tables`

`.show``external``table`*テーブル名*

**出力**

| 出力パラメーター | Type   | 説明                                                         |
|------------------|--------|---------------------------------------------------------------------|
| TableName        | string | 外部テーブルの名前                                             |
| TableType        | string | 外部テーブルのタイプ                                              |
| Folder           | string | テーブルのフォルダ                                                     |
| ドクスト文字列        | string | テーブルを表に示す文字列                                       |
| Properties       | string | テーブルの JSON シリアル化されたプロパティ (テーブルの種類に固有) |


**例:**

```kusto
.show external tables
.show external table T
```

| TableName | TableType | Folder         | ドクスト文字列 | Properties |
|-----------|-----------|----------------|-----------|------------|
| T         | BLOB      | 外部テーブル | Docs      | {}         |


### <a name="show-external-table-schema"></a>.show 外部テーブル スキーマ

* 外部テーブルのスキーマを JSON または CSL として返します。 
* [データベース モニタのアクセス許可](../management/access-control/role-based-authorization.md)が必要です。

**構文 :** 

`.show``external``as`*TableName*`json`テーブル名 | (`csl`) `table` `schema`

`.show``external``table`*テーブル名*`cslschema`

**出力**

| 出力パラメーター | Type   | 説明                        |
|------------------|--------|------------------------------------|
| TableName        | string | 外部テーブルの名前            |
| スキーマ           | string | JSON 形式のテーブルスキーマ |
| DatabaseName     | string | テーブルのデータベース名             |
| Folder           | string | テーブルのフォルダ                    |
| ドクスト文字列        | string | テーブルを表に示す文字列      |

**例:**

```kusto
.show external table T schema as JSON
```

```kusto
.show external table T schema as CSL
.show external table T cslschema
```

**出力:**

*Json：*

| TableName | スキーマ    | DatabaseName | Folder         | ドクスト文字列 |
|-----------|----------------------------------|--------------|----------------|-----------|
| T         | {"名前":"外部 BLOB"<br>"フォルダ":"外部テーブル",<br>"ドキュメント文字列":"ドキュメント",<br>"順序付けられた列":"名前":"x","タイプ":"システム.Int64","CslType":""""""""""""""""""""""""""""""""""""""タイプ":"システム.文字列"、"CslType":"文字列"、""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""" | DB           | 外部テーブル | Docs      |


*Csl：*

| TableName | スキーマ          | DatabaseName | Folder         | ドクスト文字列 |
|-----------|-----------------|--------------|----------------|-----------|
| T         | x:長い、s:文字列 | DB           | 外部テーブル | Docs      |

### <a name="drop-external-table"></a>.drop 外部テーブル

* 外部テーブルを削除する 
* この操作の後、外部テーブル定義を復元できません
* [データベース管理者の権限](../management/access-control/role-based-authorization.md)が必要です。

**構文 :**  

`.drop``external``table`*テーブル名*

**出力**

ドロップされたテーブルのプロパティを返します。 [外部テーブルの表示を参照](#show-external-tables)してください。

**例:**

```kusto
.drop external table ExternalBlob
```

| TableName | TableType | Folder         | ドクスト文字列 | スキーマ       | Properties |
|-----------|-----------|----------------|-----------|-----------------------------------------------------|------------|
| T         | BLOB      | 外部テーブル | Docs      | [{ "名前": "x","CslType": "long"}<br> { "名前" : "s" ""、"CslType": "文字列" }] | {}         |

## <a name="external-tables-in-azure-storage-or-azure-data-lake"></a>Azure ストレージまたは Azure データ レイクの外部テーブル

次のコマンドは、外部テーブルの作成方法を説明します。 テーブルは、Azure Blob ストレージ、Azure データ 湖ストア Gen1、または Azure データ 湖ストア Gen2 にあります。 
[ストレージ接続文字列](../api/connection-strings/storage.md)は、これらの各オプションの接続文字列を作成する方法を説明します。 

### <a name="create-or-alter-external-table"></a>.create または .alter 外部テーブル

**構文**

`.create` | (`.alter` `external` ) `table` *テーブル名*(*スキーマ*)  
`kind` `=` (`blob` | `adl`)  
[`partition``by`*Partition*パーティション`,`[ ..] ]  
`dataformat``=`*フォーマット*  
`(`  
*ストレージ接続文字列*`,` [ .]  
`)`  
[`with` `(``docstring` `=` [ ドキュメント`,``folder``=` *]*[*フォルダ名*] , *property_name*`=`*値*`,`.`)`]

コマンドが実行されるデータベース内に、新しい外部テーブルを作成または変更します。

**パラメーター**

* *テーブル名*- 外部テーブル名。 [エンティティ名](../query/schema-entities/entity-names.md)の規則に従う必要があります。 外部テーブルに同じデータベース内の通常のテーブルと同じ名前を付けることができません。
* *スキーマ*- 外部データ スキーマ`ColumnName:ColumnType[, ColumnName:ColumnType ...]`の形式: . 外部データ スキーマが不明な場合は[、infer_storage_schema](../query/inferstorageschemaplugin.md)プラグインを使用します。
* *パーティション*- 1 つまたは複数のパーティション定義 (オプション)。 以下のパーティション構文を参照してください。
* *形式*- データ形式。 クエリでは、イン[ジェクション形式](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)がサポートされています。 [エクスポート シナリオ](data-export/export-data-to-an-external-table.md)で外部テーブルを使用する形式は、 `CSV`、 `TSV` `JSON`、 `Parquet`、 の形式に制限されています。
* *ストレージ接続文字列*- 資格情報を含む Azure Blob ストレージ BLOB コンテナーまたは Azure Data Lake Store ファイル システム (仮想ディレクトリまたはフォルダー) への 1 つまたは複数のパス。 詳細については[、ストレージ接続文字列](../api/connection-strings/storage.md)を参照してください。 大量のデータを外部テーブルにエクスポートする場合は、ストレージの制限[を](data-export/export-data-to-an-external-table.md)避けるために、複数のストレージ アカウントを提供することを強くお勧めします。 エクスポートは、提供されたすべてのアカウント間で書き込みを分散します。 

**パーティションの構文**

[`format_datetime =` *日付時刻パーティションのフォーマット*]`bin(`*タイムスタンプ列名*,*パーティションバイタイムスパン*`)`  
|   
[*文字列形式のプレフィックス*]*文字列列名*[*文字列形式の接尾辞*]

**パーティション パラメータ**

* *日付時刻パーティションフォーマット*- 出力パスに必要なディレクトリ構造の形式 (オプション)。 パーティション分割が定義され、フォーマットが指定されていない場合、デフォルトは PartitionByTimeSpan に基づいて "yyyy/MM/dd/HH/mm" になります。 たとえば、1d でパーティション分割すると、構造は "yyyy/MM/dd" になります。 1h でパーティション分割すると、構造は "yyyy/MM/dd/HH" になります。
* *タイムスタンプ列名*- テーブルがパーティション化されている日時列。 外部テーブルにエクスポートする場合、外部テーブルスキーマ定義およびエクスポート クエリの出力に timestamp 列が存在する必要があります。
* *パーティションバイタイムスパン*- パーティションを作成するタイムスパンリテラル。
* *StringFormatPrefix* - テーブル値の前に連結された、アーティファクトパスの一部となる定数文字列リテラルです (省略可能)。
* *文字列形式の接尾辞*- テーブル値の後に連結される、アーティファクト パスの一部となる定数文字列リテラルです (省略可能)。
* *文字列列名*- テーブルがパーティション分割される文字列列。 外部テーブルスキーマ定義に文字列列が存在する必要があります。

**省略可能なプロパティ**:

| プロパティ         | Type     | 説明       |
|------------------|----------|-------------------------------------------------------------------------------------|
| `folder`         | `string` | テーブルのフォルダ                                                                     |
| `docString`      | `string` | テーブルを表に示す文字列                                                       |
| `compressed`     | `bool`   | 設定されている場合、BLOB がファイルとして`.gz`圧縮されているかどうかを示します。                  |
| `includeHeaders` | `string` | CSV または TSV BLOB の場合、BLOB にヘッダーが含まれているかどうかを示します。                     |
| `namePrefix`     | `string` | 設定されている場合は、BLOB のプレフィックスを示します。 書き込み操作では、すべての BLOB がこのプレフィックスで書き込まれます。 読み取り操作では、このプレフィックスを持つ BLOB のみが読み取られます。 |
| `fileExtension`  | `string` | 設定されている場合は、BLOB のファイル拡張子を示します。 書き込み時に、BLOB 名はこのサフィックスで終わるでしょう。 読み取り時には、このファイル拡張子を持つ BLOB のみが読み取られます。           |
| `encoding`       | `string` | テキストのエンコード方法を`UTF8NoBOM``UTF8BOM`示します。             |

> [!NOTE]
> * テーブルが存在する場合`.create`、コマンドはエラーで失敗します。 既存`.alter`のテーブルを変更するために使用します。 
> * 外部 BLOB テーブルのスキーマ、形式、またはパーティション定義の変更はサポートされていません。 

[に対](../management/access-control/role-based-authorization.md)する`.create`データベース ユーザー権限と[テーブル管理者のアクセス許可](../management/access-control/role-based-authorization.md)が`.alter`必要です。 
 
**例** 

パーティション化されていない外部テーブル。 すべてのアーティファクトは、定義されたコンテナの直下に配置されます。

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
   folder = "ExternalTables",
   namePrefix="Prefix"
)  
```

日時でパーティション化された外部テーブル。 アーティファクトは、定義されたパスの下にある"yyyy/MM/dd"形式のディレクトリに存在します。

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
   folder = "ExternalTables",
   namePrefix="Prefix"
)  
```

ディレクトリ形式が "年=yyyy/month=MM/日=dd"の日時でパーティション化された外部テーブル:

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
   folder = "ExternalTables",
   namePrefix="Prefix"
)
```

月次データ パーティションと "yyyy/MM" のディレクトリ形式を持つ外部テーブル:

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
   folder = "ExternalTables",
   namePrefix="Prefix"
)
```

2 つのパーティションを持つ外部テーブル。 ディレクトリ構造は、両方のパーティションの連結です: 形式の CustomerName に続いて、既定の日時形式。 例えば、「顧客名=ソフトワークス/2011/11/11」の例:

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

|TableName|TableType|Folder|ドクスト文字列|Properties|ConnectionStrings|メジャー グループ|
|---|---|---|---|---|---|---|
|外部複数パーティション|BLOB|外部テーブル|Docs|{"形式":"Csv""""""""""""""""":false,"圧縮タイプ":null、ファイル拡張子":"csv"、"、""""なし"、エンコーディング":null、名前プレフィックス":null}|["https://storageaccount.blob.core.windows.net/container1;*******"]}|[{"文字列形式":"顧客名={0}"列名":"顧客名","序数"、パーティションBy":"1.00:00:00"、列名":"タイムスタンプ"、序数":1}|

#### <a name="spark-virtual-columns-support"></a>Spark 仮想列のサポート

Spark からデータをエクスポートする場合、データフレーム ライターの`partitionBy`メソッドで指定されているパーティション列はデータ ファイルに書き込まれません。 これは、既に "フォルダー" 名に存在するデータ ( など )`column1=<value>/column2=<value>/`がデータを読み取ったときに認識できるため、データの重複を避けるために行われます。 ただし、Kusto では、データ自体にパーティション列が存在する必要があります。 Kusto の仮想列のサポートが計画されています。 それまでは、次の回避策を使用します: Spark からデータをエクスポートする場合は、データフレームを書き込む前に、データがパーティション化されているすべての列のコピーを作成します。

```kusto
df.withColumn("_a", $"a").withColumn("_b", $"b").write.partitionBy("_a", "_b").parquet("...")
```

Kusto で外部テーブルを定義する場合は、次の例のようにパーティション列を指定します。

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

### <a name="show-external-table-artifacts"></a>.show 外部テーブルのアーティファクト

* 特定の外部テーブルを照会するときに処理されるすべてのアイテムのリストを返します。
* [データベース ユーザーの権限](../management/access-control/role-based-authorization.md)が必要です。

**構文 :** 

`.show``external``table`*テーブル名*`artifacts`

**出力**

| 出力パラメーター | Type   | 説明                       |
|------------------|--------|-----------------------------------|
| Uri              | string | 外部ストレージアーティファクトの URI |

**例:**

```kusto
.show external table T artifacts
```

**出力:**

| Uri                                                                     |
|-------------------------------------------------------------------------|
| https://storageaccount.blob.core.windows.net/container1/folder/file.csv |

### <a name="create-external-table-mapping"></a>.create 外部テーブルマッピング

`.create``external``mapping`*MappingName**ExternalTableName*`json`*MappingInJsonFormat*外部テーブル名マッピング名マッピングInJson形式`table`

新しいマッピングを作成します。 詳細については、「データ[マッピング](./mappings.md#json-mapping)」を参照してください。

**例** 
 
```kusto
.create external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**出力例**

| 名前     | 種類 | マッピング                                                           |
|----------|------|-------------------------------------------------------------------|
| マッピング1 | JSON | [{"列名":"行番号"、列タイプ":"int","プロパティ"、"{"パス":"$.rownumber"}、{"列名":"行guid,"列タイプ":"""""""""""""""""""""""パス":"$.rowguid"}} |

### <a name="alter-external-table-mapping"></a>.alter 外部テーブル マッピング

`.alter``external``mapping`*MappingName**ExternalTableName*`json`*MappingInJsonFormat*外部テーブル名マッピング名マッピングInJson形式`table`

既存のマッピングを変更します。 
 
**例** 
 
```kusto
.alter external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**出力例**

| 名前     | 種類 | マッピング                                                                |
|----------|------|------------------------------------------------------------------------|
| マッピング1 | JSON | [{"列名":"行番号"、"列の種類":""""""""""""""""""パス":"$.rownumber"}、{"列名"""""列名"""""""""""""""""""""""パス":"$.rowguid"}} |

### <a name="show-external-table-mappings"></a>.show 外部テーブル マッピング

`.show``external``mapping`*ExternalTableName*`json`外部テーブル名*マッピング*名`table` 

`.show``external`*ExternalTableName*`json`外部テーブル名`table``mappings`

マッピング (すべてまたは名前で指定されたもの) を表示します。
 
**例** 
 
```kusto
.show external table MyExternalTable JSON mapping "Mapping1" 

.show external table MyExternalTable JSON mappings 
```

**出力例**

| 名前     | 種類 | マッピング                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| マッピング1 | JSON | [{"列名":"行番号"、"列の種類":""""""""""""""""""パス":"$.rownumber"}、{"列名"""""列名"""""""""""""""""""""""パス":"$.rowguid"}} |

### <a name="drop-external-table-mapping"></a>.drop 外部テーブル マッピング

`.drop``external``mapping`*ExternalTableName*`json`外部テーブル名*マッピング*名`table` 

データベースからマッピングを削除します。
 
**例** 
 
```kusto
.drop external table MyExternalTable JSON mapping "Mapping1" 
```

## <a name="external-sql-table"></a>外部 SQL テーブル

### <a name="create-or-alter-external-sql-table"></a>外部 SQL テーブルの作成または変更

コマンドが実行されるデータベース内の外部 SQL テーブルを作成または変更します。  

**構文**

`.create` | (`.alter` `external` ) `table` *テーブル名*([列名:列タイプ]、.)  
`kind` `=` `sql`  
`table``=`*テーブル名*  
`(`*文字列*`)`  
[`with` `(``docstring` `=` [ ドキュメント`,``folder``=` *]*[*フォルダ名*] , *property_name*`=`*値*`,`.`)`]


**パラメータ:**

* *テーブル名*- 外部テーブル名。 [エンティティ名](../query/schema-entities/entity-names.md)の規則に従う必要があります。 外部テーブルに同じデータベース内の通常のテーブルと同じ名前を付けることができません。
* *テーブル名*- SQL テーブルの名前。
* 接続文字列 *-* SQL サーバーへの接続文字列。 以下のいずれかを指定できます。 
    * **AAD 統合認証**(`Authentication="Active Directory Integrated"`): ユーザーまたはアプリケーションは、AAD から Kusto に対して認証を行い、同じトークンを使用して SQL Server ネットワーク エンドポイントにアクセスします。
    * **ユーザー名/パスワード認証**( )`User ID=...; Password=...;` 外部テーブルを[継続的なエクスポート](data-export/continuous-data-export.md)に使用する場合は、この方法を使用して認証を実行する必要があります。 

> [!WARNING]
> 接続文字列と機密情報を含むクエリは、Kusto トレースから除外されるように難読化する必要があります。 詳細については、[難読化された文字列リテラル](../query/scalar-data-types/string.md#obfuscated-string-literals)を参照してください。

**オプションのプロパティ**
| プロパティ            | Type            | 説明                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `folder`            | `string`        | テーブルのフォルダ。                  |
| `docString`         | `string`        | テーブルを表す文字列。      |
| `firetriggers`      | `true`/`false`  | の`true`場合、SQL テーブルに定義された INSERT トリガを起動するようにターゲット システムに指示します。 既定では、 `false`です。 (詳細については、「[一括挿入](https://msdn.microsoft.com/library/ms188365.aspx)」および[「システム.データ.SqlClient.SqlBulkCopy」を参照してください](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx)。 |
| `createifnotexists` | `true`/ `false` | の`true`場合、ターゲット SQL テーブルが存在しない場合は作成されます。この`primarykey`場合、主キーである結果列を示すために、プロパティを指定する必要があります。 既定では、 `false`です。  |
| `primarykey`        | `string`        | の`createifnotexists`場合`true`は、このコマンドで SQL テーブルの主キーとして使用される結果の列の名前を示します。                  |

> [!NOTE]
> * テーブルが存在する`.create`場合、コマンドはエラーで失敗します。 既存`.alter`のテーブルを変更するために使用します。 
> * 外部 SQL テーブルのスキーマまたは形式の変更はサポートされていません。 

[データベースユーザーのアクセス許可](../management/access-control/role-based-authorization.md)と、 の`.create`[テーブル管理者権限](../management/access-control/role-based-authorization.md)が`.alter`必要です。 
 
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

| TableName   | TableType | Folder         | ドクスト文字列 | Properties                            |
|-------------|-----------|----------------|-----------|---------------------------------------|
| 外部の Sql | Sql       | 外部テーブル | Docs      | {<br>  "ターゲットエンティティキンド": "sqltable"、<br>  "ターゲットエンティティ名": "MySqlTable",<br>  "ターゲットエンティティ接続文字列": "サーバー=tcp:マイサーバー.データベース.ウィンドウズ,1433;認証=アクティブディレクトリ統合;初期カタログ=マイデータベース;,,<br>  「ファイアトリガー」:真、<br>  "存在しない": 真、<br>  "主キー": "x"<br>} |

### <a name="querying-an-external-table-of-type-sql"></a>SQL タイプの外部テーブルの照会 
他の種類の外部テーブルと同様に、外部 SQL テーブルのクエリもサポートされています。 [外部テーブルのクエリを](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)参照してください。 SQL 外部テーブルクエリの実装では、SQL テーブルから完全な 'SELECT *' (または関連する列を選択) が実行され、残りのクエリは Kusto 側で実行されます。 次の外部テーブル クエリを検討してください。 

```kusto
external_table('MySqlExternalTable') | count
```

KUsto は、SQL データベースに対して 'SELECT * from TABLE' クエリを実行し、続いてクストー側でカウントを実行します。 このような場合、T-SQL で直接書き込み ('SELECT COUNT(1) FROM TABLE') 外部テーブル関数を使用する代わりに[sql_requestプラグイン](../query/sqlrequestplugin.md)を使用して実行すると、パフォーマンスが向上すると予想されます。 同様に、フィルタは SQL クエリにプッシュされません。  
クエリで Kusto 側で実行するためにテーブル全体 (または関連する列) を読み取る必要がある場合は、外部テーブルを使用して SQL テーブルを照会することをお勧めします。 Sql クエリを T-SQL で大幅に最適化できる場合は[、sql_request プラグイン](../query/sqlrequestplugin.md)を使用します。
