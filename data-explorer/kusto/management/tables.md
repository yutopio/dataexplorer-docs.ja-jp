---
title: テーブル管理 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのテーブル管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/27/2020
ms.openlocfilehash: 7b7e4c5c7111354864aa939ece76be2ab0a8ac15
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519569"
---
# <a name="tables-management"></a>テーブル管理

このトピックでは、テーブルと関連する制御コマンドのライフ サイクルについて説明します。

詳細については、下の表のリンクを選択してください。

| コマンド                                                                                                                 | Operation                       |
|--------------------------------------------------------------------------------------------------------------------------|---------------------------------|
| [`.alter table docstring`](alter-table-docstring-command.md), [`.alter table folder`](alter-table-folder-command.md)                                                                                                                                                                                                   | テーブル表示プロパティの管理 |
| [`.create ingestion mapping`](create-ingestion-mapping-command.md), [`.show ingestion mappings`](show-ingestion-mapping-command.md), [`.alter ingestion mapping`](alter-ingestion-mapping-command.md), [`.drop ingestion mapping`](drop-ingestion-mapping-command.md)                                                                    | 取り込みマッピングの管理        |
| [`.create tables`](create-tables-command.md), [`.create table`](create-table-command.md), [`.alter table`](alter-table-command.md), [`.alter-merge table`](alter-table-command.md), [`.drop tables`](drop-table-command.md), [`.drop table`](drop-table-command.md), [`.undo drop table`](undo-drop-table-command.md), [`.rename table`](rename-table-command.md) | テーブルの作成/変更/削除       |
| [`.show tables`](show-tables-command.md) [`.show table details`](show-table-details-command.md)[`.show table schema`](show-table-schema-command.md)                                                                                      | データベース内のテーブルを列挙する  |
| `.ingest`、 `.set` `.append`、、(`.set-or-append`詳細については[、データの取り込み](./data-ingestion/index.md)を参照してください)                                                                                                                                                                                      | テーブルへのデータ取り込み     |

## <a name="crud-naming-conventions-for-tables"></a>テーブルの CRUD 命名規則 
(上記の表にリンクされているセクションの詳細を参照してください。
 
| コマンド構文                             | Semantics                                                                                                             |
|--------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| `.create entityType entityName ...`        | その型と名前のエンティティが存在する場合は、エンティティを返します。 それ以外の場合は、エンティティを作成します。                          |
| `.create-merge entityType entityName...`   | その型と名前のエンティティが存在する場合は、既存のエンティティを指定されたエンティティとマージします。 それ以外の場合は、エンティティを作成します。 |
| `.alter entityType entityName ...`         | その型と名前のエンティティが存在しない場合は、エラーです。 それ以外の場合は、指定したエンティティで置き換えます。            |
| `.alter-merge entityType entityName ...`   | その型と名前のエンティティが存在しない場合は、エラーです。 それ以外の場合は、指定したエンティティとマージします。              |
| `.drop entityType entityName ...`          | その型と名前のエンティティが存在しない場合は、エラーです。 それ以外の場合は、ドロップします。                                         |
| `.drop entityType entityName ifexists ...` | その型と名前のエンティティが存在しない場合は、返します。 それ以外の場合は、ドロップします。                                        |
 
> [!NOTE]
> "マージ" は、2 つのエンティティを論理的にマージしたものです。
>
> * プロパティが一方のエンティティに対して定義されているが、もう一方のエンティティに定義されていない場合、結合されたエンティティには元の値で表示されます。
> * プロパティが両方のエンティティに対して定義され、両方に同じ値を持つ場合、そのプロパティはマージされたエンティティのその値と一緒に 1 回表示されます。
> * プロパティが両方のエンティティに対して定義されているが、値が異なる場合は、エラーが発生します。