---
title: テーブル管理-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのテーブルの管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/27/2020
ms.openlocfilehash: 4678cd23e8c92f0b53b26be965f614c009c9f0bd
ms.sourcegitcommit: 1618cbad18f92cf0cda85cb79a5cc1aa789a2db7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2020
ms.locfileid: "91615004"
---
# <a name="tables-management"></a>テーブル管理

このトピックでは、テーブルのライフサイクルと関連付けられている制御コマンドについて説明します。テーブルの探索、作成、および変更に役立ちます。

詳細については、次の表のリンクを選択してください。

| コマンド                                                                                                                 | 操作                       |
|--------------------------------------------------------------------------------------------------------------------------|---------------------------------|
| [`.alter table docstring`](alter-table-docstring-command.md), [`.alter table folder`](alter-table-folder-command.md)                                                                                                                                                                                                   | テーブル表示プロパティの管理 |
| [`.create ingestion mapping`](create-ingestion-mapping-command.md), [`.show ingestion mappings`](show-ingestion-mapping-command.md), [`.alter ingestion mapping`](alter-ingestion-mapping-command.md), [`.drop ingestion mapping`](drop-ingestion-mapping-command.md)                                                                    | インジェストマッピングの管理        |
| [`.create tables`](create-tables-command.md), [`.create table`](create-table-command.md), [`.alter table`](alter-table-command.md), [`.alter-merge table`](alter-table-command.md), [`.drop tables`](drop-table-command.md), [`.drop table`](drop-table-command.md), [`.undo drop table`](undo-drop-table-command.md), [`.rename table`](rename-table-command.md) | テーブルの作成/変更/削除       |
| [`.show tables`](show-tables-command.md) [`.show table details`](show-table-details-command.md)[`.show table schema`](show-table-schema-command.md)                                                                                      | データベース内のテーブルの列挙  |
| `.ingest`、 `.set` 、 `.append` 、 `.set-or-append` (詳細については、「 [データの取り込み](../../ingest-data-overview.md#kusto-query-language-ingest-control-commands) 」を参照してください)。                                                                                                                                                                                      | テーブルへのデータの取り込み     |
| [`.clear table data`](clear-table-data-command.md)                            | テーブルのすべてのデータをクリアします。  |

## <a name="crud-naming-conventions-for-tables"></a>テーブルの CRUD 名前付け規則 
(上記の表のリンク先のセクションの詳細を参照してください)。
 
| コマンド構文                             | セマンティクス                                                                                                             |
|--------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| `.create entityType entityName ...`        | その型と名前のエンティティが存在する場合、はエンティティを返します。 それ以外の場合は、エンティティを作成します。                          |
| `.create-merge entityType entityName...`   | その種類と名前のエンティティが存在する場合は、既存のエンティティを指定されたエンティティにマージします。 それ以外の場合は、エンティティを作成します。 |
| `.alter entityType entityName ...`         | その型と名前のエンティティが存在しない場合は、エラーです。 それ以外の場合は、指定されたエンティティで置き換えます。            |
| `.alter-merge entityType entityName ...`   | その型と名前のエンティティが存在しない場合は、エラーです。 それ以外の場合は、指定されたエンティティとマージします。              |
| `.drop entityType entityName ...`          | その型と名前のエンティティが存在しない場合は、エラーです。 それ以外の場合は削除します。                                         |
| `.drop entityType entityName ifexists ...` | その型と名前のエンティティが存在しない場合は、を返します。 それ以外の場合は削除します。                                        |
 
> [!NOTE]
> "Merge" は、2つのエンティティを論理的に結合したものです。
>
> * 1つのエンティティに対して定義されている一方のエンティティに対してプロパティが定義されていない場合は、マージされたエンティティに元の値が表示されます。
> * 両方のエンティティに対してプロパティが定義されていて、両方の値が同じである場合は、マージされたエンティティにその値が1回表示されます。
> * プロパティが両方のエンティティに対して定義されていても値が異なる場合は、エラーが発生します。