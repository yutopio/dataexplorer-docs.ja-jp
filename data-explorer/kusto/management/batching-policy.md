---
title: Kusto IngestionBatching ポリシー管理コマンド-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの IngestionBatching policy コマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 2ff3578b055fd487f3d339dc9b7fb4aa1ad11e14
ms.sourcegitcommit: d9e203a54b048030eeb6d05b01a65902ebe4e0b8
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97371443"
---
# <a name="ingestionbatching-policy-command"></a>IngestionBatching policy コマンド

[IngestionBatching ポリシー](batchingpolicy.md)は、指定された設定に従ってデータの取り込み中にデータの集計をいつ停止するかを決定するポリシーオブジェクトです。

ポリシーをに設定することができます `null` 。この場合、既定値が使用され、最大バッチ処理時間が5分に、1000項目、合計バッチサイズが1g であるか、Kusto に設定された既定のクラスター値に設定されます。

ポリシーが特定のエンティティに対して設定されていない場合は、上位階層レベルのポリシーが検索されます。すべて null に設定されている場合は、既定値が使用されます。 

**IngestionBatching の制限:**

| Type | Default | 最小値 | 最大値
|---|---|---|---|
| 項目数 | 1000 | 1 | 2000 |
| データサイズ (MB) | 1000 | 100 | 1000 |
| 時刻 | 5 分 | 10 秒 | 約 15 分 |

## <a name="displaying-the-ingestionbatching-policy"></a>IngestionBatching ポリシーを表示しています

このポリシーは、データベースまたはテーブルに対して設定でき、次のいずれかのコマンドを使用して表示されます。

* `.show` `database` *DatabaseName* `policy` `ingestionbatching`
* `.show``table` *DatabaseName* `.`  TableName `policy``ingestionbatching`

## <a name="altering-the-ingestionbatching-policy"></a>IngestionBatching ポリシーを変更する

```kusto
.alter <entity_type> <database_or_table_name> policy ingestionbatching @'<ingestionbatching policy json>'
```

複数のテーブルの IngestionBatching ポリシーを変更する (同じデータベースコンテキスト内):

```kusto
.alter tables (table_name [, ...]) policy ingestionbatching @'<ingestionbatching policy json>'
```

IngestionBatching ポリシー:

```kusto
{
  "MaximumBatchingTimeSpan": "00:05:00",
  "MaximumNumberOfItems": 500, 
  "MaximumRawDataSizeMB": 1024
}
```

* `entity_type` : テーブル、データベース
* `database_or_table`: エンティティがテーブルまたはデータベースの場合は、次のようにコマンドでその名前を指定する必要があります。 
  - `database_name` 
  - `database_name.table_name` 
  - `table_name` (特定のデータベースのコンテキストで実行されている場合)

## <a name="deleting-the-ingestionbatching-policy"></a>IngestionBatching ポリシーを削除しています

```kusto
.delete <entity_type> <database_or_table_name> policy ingestionbatching
```

**使用例**

```kusto
// Show IngestionBatching policy for table `MyTable` in database `MyDatabase`
.show table MyDatabase.MyTable policy ingestionbatching 

// Set IngestionBatching policy on table `MyTable` (in database context) to batch ingress data by 30 seconds, 500 files, or 1GB (whatever comes first)
.alter table MyTable policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:00:30", "MaximumNumberOfItems": 500, "MaximumRawDataSizeMB": 1024}'

// Set IngestionBatching policy on multiple tables (in database context) to batch ingress data by 1 minute, 20 files, or 300MB (whatever comes first)
.alter tables (MyTable1, MyTable2, MyTable3) policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:01:00", "MaximumNumberOfItems": 20, "MaximumRawDataSizeMB": 300}'

// Delete IngestionBatching policy on table `MyTable`
.delete table MyTable policy ingestionbatching

// Delete IngestionBatching policy on database `MyDatabase`
.delete database MyDatabase policy ingestionbatching
```
