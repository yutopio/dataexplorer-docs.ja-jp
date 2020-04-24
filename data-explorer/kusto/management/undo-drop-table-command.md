---
title: .undo ドロップ テーブル - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .undo ドロップ テーブルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 55fd08ce2624c522b971e1ee3862f7d11c6f679f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519552"
---
# <a name="undo-drop-table"></a>.undo ドロップ テーブル

この`.undo``drop``table`コマンドは、テーブルのドロップ操作を特定のデータベースバージョンに戻します。

**構文**

`.undo``drop`*TableName*`as``version=v`*DB_MajorVersion.DB_MinorVersion**NewTableName*テーブル名 [ 新しいテーブル名 ] DB_MajorVersion.DB_MinorVersion `table`

コマンドは、データベース コンテキストで実行する必要があります。

**戻り値**

このコマンドは、次の操作を行います。
* 元のテーブルエクステントリストを返します。
* エクステントに含まれるレコードの数をエクステントごとに指定します。
* リカバリ操作が成功したか失敗したかの場合に返します。
* 該当する場合は、失敗理由を返します。

| エクステントID                             | レコード数 | Status                   | FailureReason                                                                                                                  |
|--------------------------------------|-----------------|--------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| ef296c9e-d75d-44bc-985c-b93dd2519691 | 100             | 回復                |
| 370b30d7-cf2a-4997-986e-3d05f49c9689 | 1000            | 回復                |
| 861f18a5-6cde-4f1e-a003-a43506f9e8da | 855             | エクステントを回復できません | エクステントコンテナ: 4b47fd84-c7db-4cfb-9378-67c1de7bf154 が見つからず、そのエクステントがストレージから削除され、復元できません |

**使用例**

```
// Recover TestTable table to database version 24.3
.undo drop table TestTable version="v24.3"
```

```
// Recover TestTable table to database version 10.3 with new table name, NewTestTable (can be used if a table with the same name was already created since the drop)  
.undo drop table TestTable as NewTestTable version="v10.3"
```

**必要なデータベースバージョンを見つける方法**

ドロップ操作が実行される前に、コマンドを使用してデータベースのバージョン`.show``journal`を確認できます。

```
.show database TestDB journal | where Event == "DROP-TABLE" and EntityName == "TestTable" | project OriginalEntityVersion 
```

| 元のエンティティのバージョン |
|-----------------------|
| v24.3                 |

**制限事項**

このデータベースで Purge コマンドを実行した場合、undo drop table コマンドは、パージ実行の前のバージョンに対して実行できません。

エクステントは、エクステント コンテナのハード削除期間に達していない場合にのみ回復できます。

このコマンドには[、データベース管理者権限](../management/access-control/role-based-authorization.md)が必要です。