---
title: インジェストインラインコマンド (プッシュ)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのインジェストインラインコマンド (プッシュ) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 2b1766b295fd348639d8d91c8308a3ed0a35a3dc
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373408"
---
# <a name="the-ingest-inline-command-push"></a>インジェストインラインコマンド (push)

このコマンドは、インラインで埋め込まれているデータをコマンドテキスト自体に "プッシュ" することで、データをテーブルに取り込みします。

> [!NOTE]
> このコマンドの主な目的は、手動によるアドホックテストの目的です。
> 運用環境で使用する場合は、[ストレージからの取り込み](./ingest-from-storage.md)など、大量のデータの一括配信に適した他の取り込み方法を使用することをお勧めします。

**構文**

`.ingest``inline` `into` `table`*TableName* [ `with` `(` *IngestionPropertyName* `=` *IngestionPropertyValue* [ `,` ...] `)` ] `<|`*データ*



**引数**

* *TableName*は、データの取り込み先のテーブルの名前です。
  テーブル名は常にデータベースに対する相対参照であり、スキーママッピングオブジェクトが指定されていない場合は、そのスキーマがデータに対して想定されるスキーマです。

* *データ*は、取り込むデータコンテンツです。 インジェストプロパティによって変更されない限り、このコンテンツは CSV として解析されます。
  ほとんどのコントロールコマンドやクエリとは異なり、コマンドの*データ*部分のテキストは、言語の構文規則に従う必要はありません (たとえば、空白文字は重要で、 `//` 組み合わせはコメントとして扱われません)。

* *IngestionPropertyName*, *IngestionPropertyValue*: インジェストプロセスに影響を与える任意の数の[インジェストプロパティ](../../../ingestion-properties.md)。

**結果**

コマンドの結果は、コマンドによって生成されるデータシャード ("エクステント") の数と同数のレコードを含むテーブルです。
データシャードが生成されていない場合は、空の (ゼロ値) エクステント ID で1つのレコードが返されます。

|名前       |Type      |説明                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|ExtentId   |`guid`    |コマンドによって生成されたデータシャードの一意の識別子。|

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
It is possible to generate inline ingests commands using the Kusto.Data client library. (Note that compression does allow one to embed newlines in quoted fields) 

    Kusto.Data.Common.CslCommandGenerator.GenerateTableIngestPushCommand(tableName, compressed: true, csvData: csvStream);

-->