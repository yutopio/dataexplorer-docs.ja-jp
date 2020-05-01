---
title: . 元に戻す drop table-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの元に戻すテーブルの削除について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 2682afaa9e589a8787b7e42a83e3bb68a24a2781
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616679"
---
# <a name="undo-drop-table"></a>.undo ドロップ テーブル

`.undo`実行すると、テーブルの削除操作が特定のデータベースバージョンに戻され`drop` `table`ます。

**構文**

`.undo``drop` `as` *NewTableName* `version=v` *DB_MajorVersion.DB_MinorVersion* *TableName* TableName [newtablename] DB_MajorVersion DB_MinorVersion `table`

コマンドは、データベースコンテキストで実行する必要があります。

**戻り値**

このコマンドは、次の操作を行います。
* 元のテーブルのエクステントの一覧を返します。
* エクステントに含まれるレコードの数をエクステントごとに指定します。
* 回復操作が成功したか失敗したかを返します
* エラーの理由を返します (該当する場合)。

| ExtentId                             | NumberOfRecords | Status                   | FailureReason                                                                                                                  |
|--------------------------------------|-----------------|--------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| ef296c9e-d75d-44bc-985c-b93dd2519691 | 100             | た                |
| 370b30d7-cf2a-4997-986e-3d05f49c9689 | 1000            | た                |
| 861f18a5-6cde47 f1ea003-a43506f9e8da | 855             | エクステントを回復できません | エクステントコンテナー: 4b47fd84-c7db-4cfb-9378-67c1de7bf154 が見つかりませんでした。このエクステントはストレージから削除されたため、復元できません |

**使用例**

```kusto
// Recover TestTable table to database version 24.3
.undo drop table TestTable version="v24.3"
```

```kusto
// Recover TestTable table to database version 10.3 with new table name, NewTestTable (can be used if a table with the same name was already created since the drop)  
.undo drop table TestTable as NewTestTable version="v10.3"
```

**必要なデータベースバージョンを検索する方法**

次の`.show` `journal`コマンドを使用して、drop 操作が実行される前にデータベースのバージョンを確認できます。

```kusto
.show database TestDB journal | where Event == "DROP-TABLE" and EntityName == "TestTable" | project OriginalEntityVersion 
```

| OriginalEntityVersion |
|-----------------------|
| v 24.3                 |

**制限事項**

このデータベースに対して Purge コマンドが実行された場合、元に戻す drop table コマンドを以前のバージョンに対して実行することはできません。

エクステントを復旧できるのは、それが存在するエクステントコンテナーのハード削除期間にまだ達していない場合のみです。

コマンドには、[データベース管理者のアクセス許可](../management/access-control/role-based-authorization.md)が必要です。
