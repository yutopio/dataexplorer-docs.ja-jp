---
title: .ingest インライン コマンド (プッシュ) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .ingest インライン コマンド (プッシュ) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 47da2df6ff974afb5e91224ade695dc0863b6817
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521371"
---
# <a name="the-ingest-inline-command-push"></a>.ingest インライン コマンド (プッシュ)

このコマンドは、コマンド テキスト自体にインラインで埋め込まれたデータを "プッシュ" することによって、データをテーブルに取り込みます。

> [!NOTE]
> このコマンドの主な目的は、手動アドホックテストの目的です。
> 実稼働環境では、大量のデータのバルク配信に適した他の取[り込み方法 (ストレージからの取り込み](./ingest-from-storage.md)など) を使用することをお勧めします。

**構文**

`.ingest``inline``(`*TableName*`with``,`*IngestionPropertyName*`=`*IngestionPropertyValue*テーブル名 [ 取得プロパティ名 ] プロパティプロパティ値 [ .] `into` `table``)`] `<|` *データ*



**引数**

* *テーブル名*は、データを取り込むテーブルの名前です。
  テーブル名は常にコンテキスト内のデータベースに対して相対的であり、スキーマ マッピング オブジェクトが指定されていない場合に、そのスキーマはデータに対して想定されるスキーマです。

* *データ*は、取り込むデータコンテンツです。 インジェストプロパティによって変更されない限り、このコンテンツは CSV として解析されます。
  ほとんどの制御コマンドやクエリとは異なり、コマンドの*Data*部分のテキストは言語の構文規則に従う必要はありません (例えば、空白文字が重要であり、`//`組み合わせはコメントとして扱われません)。

* *インジェスティションプロパティ名*、*インジェスティションプロパティ値*:[インジェ](https://docs.microsoft.com/azure/data-explorer/ingestion-properties)スティプロセスに影響を与えるインジェスション プロパティの数。

**結果**

コマンドの結果は、コマンドによって生成されたデータ・シャード (「エクステント」) と同じ数のレコードを持つ表です。
データシャードが生成されていない場合は、空の (ゼロ値の) エクステント ID を持つ単一のレコードが返されます。

|名前       |Type      |説明                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|エクステントID   |`guid`    |コマンドによって生成されたデータ シャードの一意の識別子。|

**使用例**

次のコマンドは、データを 2 つの`Purchases`列 (タイプ)`SKU`と`string``Quantity`(タイプ) を`long`持つテーブル ( ) に取り込みます。

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