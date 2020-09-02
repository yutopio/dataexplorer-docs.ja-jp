---
title: externaldata オペレーター-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの外部データ演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: f86c952fdbfadd0b6ff4177ce7aa194019639b20
ms.sourcegitcommit: d54e4ebb611da2b30158720e14103e81a7daa5af
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89286426"
---
# <a name="externaldata-operator"></a>externaldata 演算子

演算子は、 `externaldata` クエリ自体でスキーマが定義されているテーブルを返します。このテーブルのデータは、Azure Blob Storage 内の blob や Azure Data Lake Storage 内のファイルなど、外部ストレージの成果物から読み込まれます。

## <a name="syntax"></a>Syntax

`externaldata``(` *ColumnName* `:` *ColumnType* [ `,` ...]`)`   
`[`*StorageConnectionString* [ `,` ...]`]`   
[ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ]

## <a name="arguments"></a>引数

* *ColumnName*、 *ColumnType*: 引数は、テーブルのスキーマを定義します。
  構文は、 [create table](../management/create-table-command.md)でテーブルを定義するときに使用する構文と同じです。

* *StorageConnectionString*: 返されるデータを保持するストレージアーティファクトを記述する [ストレージ接続文字列](../api/connection-strings/storage.md) 。

* *PropertyName*、 *PropertyValue*、...: ストレージから取得したデータを解釈する方法を説明する追加のプロパティ ( [インジェストプロパティ](../../ingestion-properties.md)に一覧表示されます)。

現在サポートされているプロパティは次のとおりです。

| プロパティ         | Type     | Description       |
|------------------|----------|-------------------|
| `format`         | `string` | データ形式。 指定しない場合、ファイル拡張子 (既定値は) からデータ形式を検出しようとし `CSV` ます。 [インジェストデータ形式](../../ingestion-supported-formats.md)のいずれかがサポートされています。 |
| `ignoreFirstRecord` | `bool` | True に設定すると、すべてのファイルの最初のレコードが無視されることを示します。 このプロパティは、ヘッダーを使用して CSV ファイルにクエリを実行する場合に便利です。 |
| `ingestionMapping` | `string` | ソースファイルのデータを演算子の結果セット内の実際の列にマップする方法を示す文字列値です。 「 [データマッピング](../management/mappings.md)」を参照してください。 |


> [!NOTE]
> この演算子は、パイプラインの入力を受け入れません。

## <a name="returns"></a>戻り値

演算子は、 `externaldata` 指定されたストレージの成果物からデータが解析された特定のスキーマのデータテーブルを返します。これは、ストレージ接続文字列によって示されます。

## <a name="examples"></a>例

**Azure Blob Storage に格納されているユーザー Id の一覧を取得します。**

次の例では、 `UserID` 列が既知の一連の id に分類され、外部ストレージファイルに1行ずつ保持される、テーブル内のすべてのレコードを検索する方法を示します。 データ形式が指定されていないため、検出されたデータ形式は `TXT` です。

```kusto
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt" 
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```

**複数のデータファイルに対してクエリを実行する**

次の例では、外部ストレージに格納されている複数のデータファイルに対してクエリを行います。

```kusto
externaldata(Timestamp:datetime, ProductId:string, ProductDescription:string)
[
  h@"https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/01/part-00000-7e967c99-cf2b-4dbb-8c53-ce388389470d.csv.gz?...SAS...",
  h@"https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/02/part-00000-ba356fa4-f85f-430a-8b5a-afd64f128ca4.csv.gz?...SAS...",
  h@"https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/03/part-00000-acb644dc-2fc6-467c-ab80-d1590b23fc31.csv.gz?...SAS..."
]
with(format="csv")
| summarize count() by ProductId
```

上の例は、 [外部テーブル](schema-entities/externaltables.md)を定義せずに、複数のデータファイルに対してクエリを実行する簡単な方法と考えることができます。

> [!NOTE]
> データのパーティション分割は演算子によって認識されません `externaldata` 。

**階層データ形式のクエリ**

、、、などの階層データ形式をクエリするには、 `JSON` `Parquet` 演算子の `Avro` `ORC` `ingestionMapping` プロパティでを指定する必要があります。 この例では、次の内容を含む Azure Blob Storage に格納されている JSON ファイルがあります。

```JSON
{
  "timestamp": "2019-01-01 10:00:00.238521",   
  "data": {    
    "tenant": "e1ef54a6-c6f2-4389-836e-d289b37bcfe0",   
    "method": "RefreshTableMetadata"   
  }   
}   
{
  "timestamp": "2019-01-01 10:00:01.845423",   
  "data": {   
    "tenant": "9b49d0d7-b3e6-4467-bb35-fa420a25d324",   
    "method": "GetFileList"   
  }   
}
...
```

演算子を使用してこのファイルを照会するには `externaldata` 、データマッピングを指定する必要があります。 このマッピングにより、JSON フィールドを演算子の結果セット列にマップする方法が決まります。

```kusto
externaldata(Timestamp: datetime, TenantId: guid, MethodName: string)
[ 
   h@'https://mycompanystorage.blob.core.windows.net/events/2020/09/01/part-0000046c049c1-86e2-4e74-8583-506bda10cca8.json?...SAS...'
]
with(format='multijson', ingestionMapping='[{"Column":"Timestamp","Properties":{"Path":"$.time"}},{"Column":"TenantId","Properties":{"Path":"$.data.tenant"}},{"Column":"MethodName","Properties":{"Path":"$.data.method"}}]')
```

`MultiJSON`ここでは、1つの JSON レコードが複数の行にまたがるため、形式が使用されます。

マッピング構文の詳細については、「[データのマッピング](../management/mappings.md)」を参照してください。
