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
ms.openlocfilehash: 4534705156669447a89cb5d85c360071dfcb2b2a
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264999"
---
# <a name="externaldata-operator"></a>externaldata 演算子

演算子は、 `externaldata` クエリ自体にスキーマが定義されていて、Azure Blob Storage の blob などの外部ストレージアーティファクトからデータを読み取るテーブルを返します。

**構文**

`externaldata``(` *ColumnName* `:` *ColumnType* [ `,` ...] `)` `[` *StorageConnectionString* `]` [ `with` `(` *Prop1* `=` *Value1* [ `,` ...] `)` ]

**引数**

* *ColumnName*、 *ColumnType*: 引数は、テーブルのスキーマを定義します。
  構文は、 [create table](../management/create-table-command.md)でテーブルを定義するときに使用する構文と同じです。

* *StorageConnectionString*:[ストレージ接続文字列](../api/connection-strings/storage.md)は、返されるデータを保持しているストレージアーティファクトを表します。

* *Prop1*、 *Value1*、...: [[インジェストのプロパティ](../../ingestion-properties.md)] に表示されている、ストレージから取得したデータを解釈する方法を説明する追加のプロパティです。
    * 現在サポートされているプロパティ: `format` および `ignoreFirstRecord` 。
    * サポートされているデータ形式:、、、、など、[インジェストデータ形式](../../ingestion-supported-formats.md)のいずれかがサポートされています `CSV` `TSV` `JSON` `Parquet` `Avro` 。

> [!NOTE]
> この演算子にはパイプライン入力がありません。

**戻り値**

演算子は、 `externaldata` 指定されたストレージの成果物からデータが解析された特定のスキーマのデータテーブルを返します。これは、ストレージ接続文字列によって示されます。

**使用例**

次の例では、 `UserID` 列が既知の一連の id に分類され、外部 blob に1行ずつ保持される、テーブル内のすべてのレコードを検索する方法を示します。
このセットはクエリによって間接的に参照されるので、大きくなることがあります。

```kusto
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt"
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```

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

上の例は、[外部テーブル](schema-entities/externaltables.md)を定義せずに、複数のデータファイルに対してクエリを実行する簡単な方法と考えることができます。

> [!NOTE]
> データのパーティション分割は演算子によって認識されません `externaldata()` 。
