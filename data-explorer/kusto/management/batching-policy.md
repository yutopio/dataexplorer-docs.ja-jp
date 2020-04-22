---
title: インジェクションバッチ処理ポリシー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのインジェスティション バッチ処理ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 232767e390669a08312f10d3999d19264fb29f26
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744314"
---
# <a name="ingestionbatching-policy"></a>インジェスティションバッチ処理ポリシー

[取り込みバッチ処理ポリシー](batchingpolicy.md)は、指定された設定に従ってデータの取り込み中にデータ集約を停止するタイミングを決定するポリシー オブジェクトです。

ポリシーを`null`に設定すると、既定値を使用する場合、バッチ処理の最大期間を 5 分、1000 項目、総バッチ サイズ 1G、または Kusto によって設定された既定のクラスター値に設定できます。

特定のエンティティに対してポリシーが設定されていない場合は、より高い階層レベルのポリシーが検索されます。 

ポリシーの下限は 10 秒であり、15 分より大きい値を使用することはお勧めできません。

## <a name="displaying-the-ingestionbatching-policy"></a>インジェスティションバッチ処理ポリシーの表示

ポリシーはデータベースまたはテーブルに設定でき、次のいずれかのコマンドを使用して表示されます。

* `.show``database`*データベース名*`policy``ingestionbatching`
* `.show``table`*データベース名*`.`*テーブル名*`policy``ingestionbatching`

## <a name="altering-the-ingestionbatching-policy"></a>インジェスレーションバッチ処理ポリシーの変更

```kusto
.alter <entity_type> <database_or_table_name> policy ingestionbatching @'<ingestionbatching policy json>'
```

複数のテーブル (同じデータベース コンテキスト内) のインジェスティニングバッチ処理ポリシーの変更:

```kusto
.alter tables (table_name [, ...]) policy ingestionbatching @'<ingestionbatching policy json>'
```

インジェスティションバッチ処理ポリシー:

```kusto
{
  "MaximumBatchingTimeSpan": "00:05:00",
  "MaximumNumberOfItems": 500, 
  "MaximumRawDataSizeMB": 1024
}
```

* `entity_type`: テーブル、データベース
* `database_or_table`: entity がテーブルまたはデータベースの場合、その名前は次のようにコマンドで指定する必要があります。 
  - `database_name` または 
  - `database_name.table_name` または 
  - `table_name`(特定のデータベースのコンテキストで実行する場合)

## <a name="deleting-the-ingestionbatching-policy"></a>インジェスティションバッチ処理ポリシーの削除

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
