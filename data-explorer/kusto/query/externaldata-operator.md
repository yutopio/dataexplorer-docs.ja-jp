---
title: 外部データオペレーター - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの外部データ オペレーターについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 0a4a151d1fd052e27e4daf76539ef6dbd9d94c83
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515472"
---
# <a name="externaldata-operator"></a>externaldata 演算子

この`externaldata`演算子は、スキーマがクエリ自体で定義され、データが外部ストレージアーティファクト (Azure BLOB ストレージ内の BLOB など) から読み取られるテーブルを返します。

> [!NOTE]
> この演算子にはパイプライン入力がありません。

**構文**

`externaldata``(`*列名*`:`*列タイプ*`,` [ ..]`)``]``with``(``=`*Value1*`,`*Prop1**StorageConnectionString*ストレージ接続文字列 [ Prop1 値 1 ] `[``)`]

**引数**

* *列名*、*列タイプ*: テーブルのスキーマを定義します。
  構文は[、.create table](../management/create-table-command.md)でテーブルを定義するときに使用する構文と同じです。

* *ストレージ接続文字列*:[ストレージ接続文字列](../api/connection-strings/storage.md)は、返されるデータを保持しているストレージ アーティファクトを表します。

* *Prop1*、 *Value1*、.: 取得プロパティにリストされているストレージから取得したデータを解釈する方法を説明する追加[のプロパティ](../management/data-ingestion/index.md)。
    * 現在サポートされているプロパティ`format`:`ignoreFirstRecord`および .
    * サポートされているデータ形式 : イン[ジェクション データ形式](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)は`tsv`、 、 `json`、 `parquet` `avro`、 など、サポートされます。 `csv`

**戻り値**

この`externaldata`演算子は、指定されたスキーマのデータ テーブルを返し、そのデータは、ストレージ接続文字列で示された指定されたストレージ アーティファクトから解析されます。

**例**

次の例は、列が`UserID`既知の ID セットに分類され、外部 BLOB 内で 1 行に 1 つずつ保持されるテーブル内のすべてのレコードを検索する方法を示しています。
セットはクエリによって間接的に参照されるため、非常に大きくなる可能性があります。

```
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt"
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```