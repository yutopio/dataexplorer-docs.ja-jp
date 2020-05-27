---
title: externaldata オペレーター-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの externaldata 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: e35245cf767e3cf82ab61d5ce0704015d996cd7c
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011378"
---
# <a name="externaldata-operator"></a>externaldata 演算子

演算子は、 `externaldata` クエリ自体でスキーマが定義され、外部ストレージアーティファクト (Azure Blob Storage 内の blob など) からデータを読み取るテーブルを返します。

> [!NOTE]
> この演算子にはパイプライン入力がありません。

**構文**

`externaldata``(` *ColumnName* `:` *ColumnType* [ `,` ...] `)` `[` *StorageConnectionString* `]` [ `with` `(` *Prop1* `=` *Value1* [ `,` ...] `)` ]

**引数**

* *ColumnName*、 *ColumnType*: テーブルのスキーマを定義します。
  構文は、 [create table](../management/create-table-command.md)でテーブルを定義するときに使用する構文と同じです。

* *StorageConnectionString*:[ストレージ接続文字列](../api/connection-strings/storage.md)は、返されるデータを保持しているストレージアーティファクトを表します。

* *Prop1*、 *Value1*、...: [[インジェストのプロパティ](../../ingestion-properties.md)] に表示されている、ストレージから取得したデータを解釈する方法を説明する追加のプロパティです。
    * 現在サポートされているプロパティ: `format` および `ignoreFirstRecord` 。
    * サポートされているデータ形式:、、、、など、[インジェストデータ形式](../../ingestion-supported-formats.md)のいずれかがサポートされています `csv` `tsv` `json` `parquet` `avro` 。

**戻り値**

演算子は、 `externaldata` 指定されたスキーマのデータテーブルを返します。このテーブルには、ストレージ接続文字列で示されている指定のストレージアーティファクトからデータが解析されています。

**例**

次の例では、 `UserID` 列が既知の一連の id に分類され、外部 blob に1行ずつ保持される、テーブル内のすべてのレコードを検索する方法を示します。
このセットはクエリによって間接的に参照されるため、非常に大きくなる可能性があります。

```
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt"
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```