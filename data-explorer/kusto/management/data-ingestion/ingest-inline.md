---
title: 取り込みインラインコマンド (プッシュ)-Azure データエクスプローラー
description: この記事では、. インジェスト inline コマンド (push) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 2ac3a9a414d31492917cfb1768ce7bb1d7d8abb1
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257928"
---
# <a name="ingest-inline-command-push"></a>。インジェストインラインコマンド (push)

このコマンドは、インラインで埋め込まれているデータをコマンドテキスト自体に "プッシュ" することで、データをテーブルに取り込みします。

> [!NOTE]
> このコマンドは、手動によるアドホックテストに使用されます。
> 実稼働環境で使用する場合は、[ストレージからの取り込み](./ingest-from-storage.md)など、大量のデータの一括配信に適した他のインジェスト方法を使用することをお勧めします。

**構文**

`.ingest``inline` `into` `table`*TableName* [ `with` `(` *IngestionPropertyName* `=` *IngestionPropertyValue* [ `,` ...] `)` ] `<|`*データ*

**引数**

* *TableName*は、データの取り込み先となるテーブルの名前です。
  名前は、常にデータベースに対してコンテキストで相対的に指定されます。
  テーブルスキーマは、スキーママッピングオブジェクトが指定されていない場合に、データに対して想定されるスキーマです。

* *データ*は、取り込むデータコンテンツです。 インジェストプロパティによって変更されない限り、このコンテンツは CSV として解析されます。
 
> [!NOTE]
> ほとんどのコントロールコマンドやクエリとは異なり、コマンドの*データ*部分のテキストは、言語の構文規則に従う必要はありません。 たとえば、空白文字が重要な場合や、 `//` 組み合わせがコメントとして扱われない場合などです。

* *IngestionPropertyName*, *IngestionPropertyValue*: インジェストプロセスに影響を与える任意の数の[インジェストプロパティ](../../../ingestion-properties.md)。

**結果**

コマンドの結果は、生成されたデータシャード ("エクステント") の数と同数のレコードを含むテーブルになります。
データシャードが生成されない場合は、空の (ゼロ値) エクステント ID で1つのレコードが返されます。

|名前       |Type      |説明                                                 |
|-----------|----------|------------------------------------------------------------|
|ExtentId   |`guid`    |コマンドによって生成されたデータシャードの一意の識別子|

**使用例**

次のコマンドは、 `Purchases` `SKU` (型 `string` ) と `Quantity` (型の) 2 つの列を持つテーブル () にデータを取り込みし `long` ます。

```kusto
.ingest inline into table Purchases <|
Shoes,1000
Wide Shoes,50
"Coats, black",20
"Coats with ""quotes""",5
```

<!--
You can generate inline ingests commands using the Kusto.Data client library. 
(Note that compression does let you embed new lines in quoted fields) 

    Kusto.Data.Common.CslCommandGenerator.GenerateTableIngestPushCommand(tableName, compressed: true, csvData: csvStream);

-->
