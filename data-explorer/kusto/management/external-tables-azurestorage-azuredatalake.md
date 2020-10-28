---
title: Azure Storage または Azure Data Lake で外部テーブルを作成および変更する-Azure データエクスプローラー
description: この記事では、Azure Storage または Azure Data Lake で外部テーブルを作成および変更する方法について説明します
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 1d0625c949fe563084caeec936e3433c9ee70f5e
ms.sourcegitcommit: ef3d919dee27c030842abf7c45c9e82e6e8350ee
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92630111"
---
# <a name="create-and-alter-external-tables-in-azure-storage-or-azure-data-lake"></a>Azure Storage または Azure Data Lake の外部テーブルを作成および変更する

次のコマンドでは、Azure Blob Storage、Azure Data Lake Store Gen1、または Azure Data Lake Store Gen2 にある外部テーブルを作成する方法について説明します。 

外部 Azure Storage テーブル機能の概要については、「 [Azure データエクスプローラーを使用した Azure Data Lake でのデータのクエリ](../../data-lake-query-data.md)」を参照してください。

## <a name="create-or-alter-external-table"></a>。外部テーブルを作成または変更します。

**構文**

( `.create`  |  `.alter`  |  `.create-or-alter` ) `external` `table` *[TableName](#table-name)* `(` *[スキーマ](#schema)*`)`  
`kind` `=` (`blob` | `adl`)  
[ `partition` `by` `(` *[パーティション](#partitions)* `)` [ `pathformat` `=` `(` *[pathformat](#path-format)* `)` ]]  
`dataformat``=` *[形式](#format)*  
`(`*[StorageConnectionString](#connection-string)* [ `,` ...]`)`   
[ `with` `(` *[PropertyName](#properties)* `=` *[Value](#properties)* `,` ... `)` ]  

コマンドが実行されるデータベース内の新しい外部テーブルを作成または変更します。

> [!NOTE]
> * テーブルが存在する場合、 `.create` コマンドは失敗し、エラーが表示されます。 `.create-or-alter`既存の `.alter` テーブルを変更するには、またはを使用します。
> * 外部 blob テーブルのスキーマ、形式、またはパーティション定義の変更はサポートされていません。 
> * 操作には、の [データベースユーザー権限](../management/access-control/role-based-authorization.md) `.create` と、の [table admin 権限](../management/access-control/role-based-authorization.md) が必要です `.alter` 。 

**パラメーター**

<a name="table-name"></a>
*テーブル*

[エンティティ名](../query/schema-entities/entity-names.md)の規則に準拠している外部テーブル名。
外部テーブルは、同じデータベース内の通常のテーブルと同じ名前にすることはできません。

<a name="schema"></a>
*スキーマ*

外部データスキーマについては、次の形式を使用して説明します。

&nbsp;&nbsp;*ColumnName* `:`*ColumnType* [ `,` *ColumnName* `:` *ColumnType* ...]

*ColumnName* はエンティティの [名前付け](../query/schema-entities/entity-names.md)規則に準拠し、 *ColumnType* は [サポートされているデータ型](../query/scalar-data-types/index.md)の1つです。

> [!TIP]
> 外部データスキーマが不明な場合は、 [推定 \_ ストレージ \_ スキーマ](../query/inferstorageschemaplugin.md) プラグインを使用します。これは、外部ファイルの内容に基づいてスキーマを推論するのに役立ちます。

<a name="partitions"></a>
*パーティション*

外部テーブルをパーティション分割する列のコンマ区切りの一覧です。 パーティション列は、データファイル自体に存在することも、ファイルパスの一部を使用することもできます ( [仮想列](#virtual-columns)の詳細についてはこちらを参照してください)。

パーティションの一覧は、次のいずれかの形式を使用して指定されたパーティション列の任意の組み合わせです。

* [仮想列](#virtual-columns)を表すパーティション。

  *PartitionName* `:` (`datetime` | `string`)

* 文字列の列の値に基づいてパーティション分割します。

  *PartitionName* `:``string` `=` *ColumnName*

* 文字列型の列値の [ハッシュ](../query/hashfunction.md)(剰余) に基づいてパーティション *分割します* 。

  *PartitionName* `:``long` `=` `hash``(` *ColumnName* `,` *数値*`)`

* Datetime 列の切り捨てられた値に基づくパーティション。 [Startofyear](../query/startofyearfunction.md)、 [startofmonth](../query/startofmonthfunction.md)、 [startofweek](../query/startofweekfunction.md)、 [startofday](../query/startofdayfunction.md) 、または[bin](../query/binfunction.md)関数に関するドキュメントを参照してください。

  *PartitionName* `:``datetime` `=` ( `startofyear` \| `startofmonth` \| `startofweek` \| `startofday` ) `(` *ColumnName*`)`  
  *PartitionName* `:``datetime` `=` `bin``(` *ColumnName* `,` *TimeSpan*`)`

パーティション分割定義の正確性を確認するには、外部テーブルを作成するときにプロパティを使用し `sampleUris` ます。

<a name="path-format"></a>
*PathFormat*

外部データ URI ファイルパス形式。パーティションに加えて指定できます。 パスの形式は、パーティション要素とテキスト区切りのシーケンスです。

&nbsp;&nbsp;[ *Stringseparator* ] *Partition* [ *stringseparator* ] [ *partition* [ *stringseparator* ]...]  

ここで、 *partition* は in 句で宣言されたパーティションを指し `partition` `by` 、 *stringseparator* は引用符で囲まれた任意のテキストです。 連続するパーティション要素は、 *Stringseparator* を使用して別に設定する必要があります。

元のファイルパスプレフィックスは、文字列としてレンダリングされ、対応するテキスト区切り記号で区切られたパーティション要素を使用して作成できます。 Datetime パーティション値の表示に使用する形式を指定するには、次のマクロを使用できます。

&nbsp;&nbsp;`datetime_pattern``(` *DateTimeFormat* `,` *PartitionName*`)`  

*DateTimeFormat* は .net 形式の仕様に準拠しており、拡張機能を使用すると、書式指定子を中かっこで囲むことができます。 たとえば、次の2つの形式は同等です。

&nbsp;&nbsp;`'year='yyyy'/month='MM` そして `year={yyyy}/month={MM}`

既定では、datetime 値は次の形式で表示されます。

| パーティション関数    | 既定の形式 |
|-----------------------|----------------|
| `startofyear`         | `yyyy`         |
| `startofmonth`        | `yyyy/MM`      |
| `startofweek`         | `yyyy/MM/dd`   |
| `startofday`          | `yyyy/MM/dd`   |
| `bin(`*項目*`, 1d)` | `yyyy/MM/dd`   |
| `bin(`*項目*`, 1h)` | `yyyy/MM/dd/HH` |
| `bin(`*項目*`, 1m)` | `yyyy/MM/dd/HH/mm` |

*Pathformat* を外部テーブル定義から省略した場合、すべてのパーティションが定義されている順序とまったく同じ順序で区切られていると見なされ `/` ます。 パーティションは、既定の文字列表現を使用してレンダリングされます。

パス形式の定義の正確性を確認するには、外部テーブルを作成するときにプロパティを使用し `sampleUris` ます。

<a name="format"></a>
*形式*

データ形式。 [取り込み形式](../../ingestion-supported-formats.md)のいずれかです。

> [!NOTE]
> [エクスポートシナリオ](data-export/export-data-to-an-external-table.md)での外部テーブルの使用は `CSV` 、、 `TSV` 、 `JSON` およびの各形式に制限され `Parquet` ます。

<a name="connection-string"></a>
*StorageConnectionString*

Blob コンテナーまたは Azure Data Lake Store ファイルシステム (仮想ディレクトリまたはフォルダー) を Azure Blob Storage するための1つ以上のパス (資格情報を含む)。
詳細については、「 [ストレージ接続文字列](../api/connection-strings/storage.md) 」を参照してください。

> [!TIP]
> 大量のデータを外部テーブルに [エクスポート](data-export/export-data-to-an-external-table.md) するときにストレージの調整を回避するには、ストレージアカウントを1つ以上指定します。 エクスポートすると、提供されたすべてのアカウント間の書き込みが分散されます。 

<a name="properties"></a>
*省略可能なプロパティ*

| プロパティ         | Type     | 説明       |
|------------------|----------|-------------------------------------------------------------------------------------|
| `folder`         | `string` | テーブルのフォルダー                                                                     |
| `docString`      | `string` | テーブルをドキュメント化する文字列                                                       |
| `compressed`     | `bool`   | 設定すると、ファイルがファイルとして圧縮されるかどうかを示します `.gz` ( [エクスポートシナリオ](data-export/export-data-to-an-external-table.md) でのみ使用)。 |
| `includeHeaders` | `string` | 区切られたテキスト形式 (CSV、TSV、...) の場合は、ファイルにヘッダーが含まれているかどうかを示します。 指定できる値は `All` 、(すべてのファイルにヘッダーが含まれている)、 `FirstFile` (フォルダー内の最初のファイルにヘッダーが含まれている)、 `None` (ヘッダーを含むファイルはありません) のいずれかです。 |
| `namePrefix`     | `string` | 設定した場合、ファイルのプレフィックスを示します。 書き込み操作では、すべてのファイルがこのプレフィックスを使用して書き込まれます。 読み取り操作では、このプレフィックスを持つファイルだけが読み取られます。 |
| `fileExtension`  | `string` | 設定すると、ファイルの拡張子を示します。 書き込み時には、ファイル名の末尾がこのサフィックスになります。 読み取り時には、このファイル拡張子を持つファイルのみが読み取られます。           |
| `encoding`       | `string` | テキストのエンコード方法を示します。 `UTF8NoBOM` (既定値) または `UTF8BOM` 。             |
| `sampleUris`     | `bool`   | 設定した場合、コマンドの結果には、外部テーブルの定義で想定される外部データファイルの URI の例がいくつか示されます (サンプルは2番目の結果テーブルに返されます)。 このオプションは、 *[パーティション](#partitions)* と *[pathformat](#path-format)* パラメーターが正しく定義されているかどうかを検証するのに役立ちます。 |
| `validateNotEmpty` | `bool`   | 設定すると、接続文字列にコンテンツが含まれているかどうかが検証されます。 指定された URI の場所が存在しない場合、またはアクセスするための十分なアクセス許可がない場合、コマンドは失敗します。 |

> [!TIP]
> `namePrefix`クエリ中のデータファイルフィルタリングでのロールおよびプロパティの詳細について `fileExtension` は、「[ファイルフィルタリングロジック](#file-filtering)」を参照してください。
 
<a name="examples"></a>
**例** 

パーティション分割されていない外部テーブルです。 データファイルは、定義されているコンテナーの直下に配置されることが予想されます。

```kusto
.create external table ExternalTable (x:long, s:string)  
kind=blob 
dataformat=csv 
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey' 
) 
```

日付でパーティション分割された外部テーブル。 日付ファイルは、既定の datetime 形式のディレクトリに配置されることが想定されてい `yyyy/MM/dd` ます。

```kusto
.create external table ExternalTable (Timestamp:datetime, x:long, s:string) 
kind=adl
partition by (Date:datetime = bin(Timestamp, 1d)) 
dataformat=csv 
( 
   h@'abfss://filesystem@storageaccount.dfs.core.windows.net/path;secretKey'
)
```

月でパーティション分割された外部テーブル。ディレクトリ形式は `year=yyyy/month=MM` 次のとおりです。

```kusto
.create external table ExternalTable (Timestamp:datetime, x:long, s:string) 
kind=blob 
partition by (Month:datetime = startofmonth(Timestamp)) 
pathformat = (datetime_pattern("'year='yyyy'/month='MM", Month)) 
dataformat=csv 
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey' 
) 
```

最初に顧客名でパーティション分割され、次に日付によって分割された外部テーブル。 次のようなディレクトリ構造が必要です `customer_name=Softworks/2019/02/01` 。

```kusto
.create external table ExternalTable (Timestamp:datetime, CustomerName:string) 
kind=blob 
partition by (CustomerNamePart:string = CustomerName, Date:datetime = startofday(Timestamp)) 
pathformat = ("customer_name=" CustomerNamePart "/" Date)
dataformat=csv 
(  
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey' 
)
```

最初に顧客名ハッシュ (剰余 10) によってパーティション分割され、次に日付でパーティション分割された外部テーブル。 たとえば、ディレクトリ構造が必要です `customer_id=5/dt=20190201` 。 データファイル名の末尾に拡張子が付いている `.txt` :

```kusto
.create external table ExternalTable (Timestamp:datetime, CustomerName:string) 
kind=blob 
partition by (CustomerId:long = hash(CustomerName, 10), Date:datetime = startofday(Timestamp)) 
pathformat = ("customer_id=" CustomerId "/dt=" datetime_pattern("yyyyMMdd", Date)) 
dataformat=csv 
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with (fileExtension = ".txt")
```

**サンプル出力**

|TableName|TableType|フォルダー|DocString|Properties|ConnectionStrings|メジャー グループ|PathFormat|
|---------|---------|------|---------|----------|-----------------|----------|----------|
|ExternalTable|BLOB|ExternalTables|Docs|{"Format": "Csv", "圧縮": false, "CompressionType": null, "FileExtension": null, "IncludeHeaders": "None", "Encoding": null, "NamePrefix": null}|["https://storageaccount.blob.core.windows.net/container1;\*\*\*\*\*\*\*"]|[{"Mod":10, "Name": "CustomerId"、"ColumnName": "CustomerId"、"Ordinal": 0}、{"Function": "StartOfDay"、"Name": "Date"、"ColumnName": "Timestamp"、"Ordinal": 1}]|"customer \_ id =" CustomerId "/dt =" datetime \_ pattern ("yyyyMMdd", Date)|

<a name="virtual-columns"></a>
**仮想列**

データが Spark からエクスポートされると、パーティション列 (データフレーム writer のメソッドで指定 `partitionBy` ) はデータファイルに書き込まれません。 このプロセスでは、"フォルダー" という名前のデータが既に存在するため、データの重複が回避されます。 たとえば、 `column1=<value>/column2=<value>/` 、、および Spark は、読み取り時にそれを認識できます。

外部テーブルでは、仮想列を指定するための次の構文がサポートされています。

```kusto
.create external table ExternalTable (EventName:string, Revenue:double)  
kind=blob  
partition by (CustomerName:string, Date:datetime)  
pathformat = ("customer=" CustomerName "/date=" datetime_pattern("yyyyMMdd", Date))  
dataformat=parquet
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
```

<a name="file-filtering"></a>
**ファイルフィルタリングロジック**

外部テーブルに対してクエリを実行すると、クエリエンジンによって、関連性のない外部ストレージファイルが除外され、パフォーマンスが向上します。 ファイルを反復処理し、ファイルを処理する必要があるかどうかを判断するプロセスを次に示します。

1. ファイルが存在する場所を表す URI パターンを作成します。 最初、URI パターンは、外部テーブル定義の一部として指定された接続文字列に相当します。 パーティションが定義されている場合は、 *[Pathformat](#path-format)* を使用してレンダリングされた後、URI パターンに追加されます。

2. 作成された URI パターンの下にあるすべてのファイルについて、次のことを確認します。

   * パーティション値は、クエリで使用される述語と一致します。
   * `NamePrefix`このようなプロパティが定義されている場合、Blob 名はで始まります。
   * `FileExtension`このようなプロパティが定義されている場合、Blob 名はで終了します。

すべての条件が満たされると、ファイルはクエリエンジンによってフェッチおよび処理されます。

> [!NOTE]
> 初期 URI パターンは、クエリ述語値を使用して作成されます。 これは、文字列値の限定されたセット、およびクローズされた時間範囲に最適です。

## <a name="show-external-table-artifacts"></a>。外部テーブルの成果物を表示する

指定された外部テーブルに対してクエリを実行するときに処理されるすべてのファイルの一覧を返します。

> [!NOTE]
> 操作には、 [データベースユーザーのアクセス許可](../management/access-control/role-based-authorization.md)が必要です。

**構文 :** 

`.show``external` `table` *TableName* `artifacts` [ `limit` *MaxResults* ]

ここで、 *MaxResults* は省略可能なパラメーターであり、結果の数を制限するように設定できます。

**出力**

| 出力パラメーター | Type   | 説明                       |
|------------------|--------|-----------------------------------|
| URI              | string | 外部ストレージデータファイルの URI |
| サイズ             | long   | ファイルの長さ (バイト単位)              |
| Partition        | 動的 | パーティション分割された外部テーブルのファイルパーティションを記述する動的オブジェクト |

> [!TIP]
> 外部テーブルによって参照されるすべてのファイルに対する反復処理は、ファイルの数によっては非常にコストがかかることがあります。 `limit`URI の例をいくつか確認するだけの場合は、パラメーターを使用してください。

**例:**

```kusto
.show external table T artifacts
```

**出力:**

| Uri                                                                     | サイズ | Partition |
|-------------------------------------------------------------------------| ---- | --------- |
| `https://storageaccount.blob.core.windows.net/container1/folder/file.csv` | 10743 | `{}`   |


パーティションテーブルの場合、 `Partition` 列には抽出されたパーティションの値が含まれます。

**出力:**

| Uri                                                                     | サイズ | Partition |
|-------------------------------------------------------------------------| ---- | --------- |
| `https://storageaccount.blob.core.windows.net/container1/customer=john.doe/dt=20200101/file.csv` | 10743 | `{"Customer": "john.doe", "Date": "2020-01-01T00:00:00.0000000Z"}` |


## <a name="create-external-table-mapping"></a>。外部テーブルマッピングを作成します。

`.create``external` `table` *Externaltablename* `json` `mapping` *MappingName* *MappingInJsonFormat*

新しいマッピングを作成します。 詳細については、「 [データのマッピング](./mappings.md#json-mapping)」を参照してください。

**例** 
 
```kusto
.create external table MyExternalTable json mapping "Mapping1" '[{"Column": "rownumber", "Properties": {"Path": "$.rownumber"}}, {"Column": "rowguid", "Properties": {"Path": "$.rowguid"}}]'
```

**出力例**

| 名前     | 種類 | マッピング                                                           |
|----------|------|-------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "rownumber", "Properties": {"=": "$. rownumber"}}, {"ColumnName": "rowguid", "Properties": {"Path": "$. rowguid"}}] |

## <a name="alter-external-table-mapping"></a>。外部テーブルマッピングを変更します。

`.alter``external` `table` *Externaltablename* `json` `mapping` *MappingName* *MappingInJsonFormat*

既存のマッピングを変更します。 
 
**例** 
 
```kusto
.alter external table MyExternalTable json mapping "Mapping1" '[{"Column": "rownumber", "Properties": {"Path": "$.rownumber"}}, {"Column": "rowguid", "Properties": {"Path": "$.rowguid"}}]'
```

**出力例**

| 名前     | 種類 | マッピング                                                                |
|----------|------|------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "rownumber", "Properties": {"=": "$. rownumber"}}, {"ColumnName": "rowguid", "Properties": {"Path": "$. rowguid"}}] |

## <a name="show-external-table-mappings"></a>。外部テーブルマッピングを表示します。

`.show``external` `table` *Externaltablename* `json` `mapping` *MappingName* 

`.show``external` `table` *ExternalTableName* Externaltablename `json``mappings`

マッピング (すべてまたは名前で指定したもの) を表示します。
 
**例** 
 
```kusto
.show external table MyExternalTable json mapping "Mapping1" 

.show external table MyExternalTable json mappings 
```

**出力例**

| 名前     | 種類 | マッピング                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName": "rownumber", "Properties": {"=": "$. rownumber"}}, {"ColumnName": "rowguid", "Properties": {"Path": "$. rowguid"}}] |

## <a name="drop-external-table-mapping"></a>。外部テーブルマッピングを削除します。

`.drop``external` `table` *Externaltablename* `json` `mapping` *MappingName* 

データベースからマッピングを削除します。
 
**例** 
 
```kusto
.drop external table MyExternalTable json mapping "Mapping1" 
```
## <a name="next-steps"></a>次のステップ

* [外部テーブル](../../data-lake-query-data.md)に対してクエリを実行します。
* [外部テーブルにデータをエクスポート](data-export/export-data-to-an-external-table.md)します。
* [外部テーブルに連続データをエクスポート](data-export/continuous-data-export.md)します。
