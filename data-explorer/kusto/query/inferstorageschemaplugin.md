---
title: infer_storage_schemaプラグイン - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーinfer_storage_schemaプラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 6c4543a3b029017067867bb70d913509941c332e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513908"
---
# <a name="infer_storage_schema-plugin"></a>infer_storage_schemaプラグイン

このプラグインは、外部データのスキーマを推論し、それを CSL スキーマ文字列として返[し、外部テーブルを作成](../management/externaltables.md#create-or-alter-external-table)するときに使用できます。

```kusto
let options = dynamic({
  'StorageContainers': [
    h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
  ],
  'DataFormat': 'parquet',
  'FileExtension': '.parquet'
});
evaluate infer_storage_schema(options)
```

**構文**

`evaluate` `infer_storage_schema(` *Options* `)`

**引数**

単一の*Options*引数は、要求`dynamic`のプロパティを指定するプロパティ バッグを保持する型の定数値です。

|名前                    |必須|説明|
|------------------------|--------|-----------|
|`StorageContainers`|はい|格納されたデータ 成果物のプレフィックス URI を表す[ストレージ接続文字列](../api/connection-strings/storage.md)の一覧|
|`DataFormat`|はい|サポートされている[データ形式](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)の 1 つ 。|
|`FileExtension`|いいえ|このファイル拡張子で終わるファイルのみをスキャンします。 必須ではありませんが、指定するとプロセスが高速化される(またはデータ読み取り問題が発生する可能性があります)|
|`FileNamePrefix`|いいえ|このプレフィックスで始まるファイルのみをスキャンします。 必須ではありませんが、指定するとプロセスが高速化される可能性があります。|
|`Mode`|いいえ|スキーマ推論戦略の 1 つ、 `any` `last`、 `all`、 。 (最初に見つかった) ファイル、最後に書き込まれたファイル、またはすべてのファイルからそれぞれデータ スキーマを推測します。 既定値は `last` です。|

**戻り値**

プラグイン`infer_storage_schema`は、CSL スキーマ文字列を保持する単一の行/列を含む単一の結果表を返します。

> [!NOTE]
> * スキーマ推論戦略 'all' は、見つかった*すべての*成果物から読み取り、スキーマをマージすることを意味するので、非常に「コストがかかる」操作です。
> * 返される型の中には、型が誤った推測 (またはスキーマのマージ プロセスの結果として) の結果として実際のものではないものがあります。 このため、外部テーブルを作成する前に結果を慎重に確認することがアドバイスされます。

**例**

```kusto
let options = dynamic({
  'StorageContainers': [
    h@'https://storageaccount.blob.core.windows.net/MovileEvents/2015;secretKey'
  ],
  'FileExtension': '.parquet',
  'FileNamePrefix': 'part-',
  'DataFormat': 'parquet'
});
evaluate infer_storage_schema(options)
```

*結果*

|をクリックします。|
|---|
|app_id:文字列,user_id,長,event_time:日時,国:文字列,city:string, device_type:文字列, device_vendor:文字列, ad_network:文字列, キャンペーン:文字列, site_id:文字列, event_type:文字列, event_name:文字列, 有機:文字列, days_from_install:int, 収益: real|