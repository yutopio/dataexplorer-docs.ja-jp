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
ms.openlocfilehash: ed70e01f7d955ba92806e7e11f740490e87cc664
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011450"
---
# <a name="tables-management"></a>テーブル管理

このトピックでは、テーブルと関連付けられているコントロールコマンドのライフサイクルについて説明します。

詳細については、次の表のリンクを選択してください。

| コマンド                                                                                                                 | 操作                       |
|--------------------------------------------------------------------------------------------------------------------------|---------------------------------|
| [`.alter table docstring`](alter-table-docstring-command.md), [`.alter table folder`](alter-table-folder-command.md)                                                                                                                                                                                                   | テーブル表示プロパティの管理 |
| [`.create ingestion mapping`](create-ingestion-mapping-command.md), [`.show ingestion mappings`](show-ingestion-mapping-command.md), [`.alter ingestion mapping`](alter-ingestion-mapping-command.md), [`.drop ingestion mapping`](drop-ingestion-mapping-command.md)                                                                    | インジェストマッピングの管理        |
| [`.create tables`](create-tables-command.md), [`.create table`](create-table-command.md), [`.alter table`](alter-table-command.md), [`.alter-merge table`](alter-table-command.md), [`.drop tables`](drop-table-command.md), [`.drop table`](drop-table-command.md), [`.undo drop table`](undo-drop-table-command.md), [`.rename table`](rename-table-command.md) | テーブルの作成/変更/削除       |
| [`.show tables`](show-tables-command.md) [`.show table details`](show-table-details-command.md)[`.show table schema`](show-table-schema-command.md)                                                                                      | データベース内のテーブルの列挙  |
| `.ingest`、 `.set` 、 `.append` 、 `.set-or-append` (詳細については、「[データの取り込み](../../ingest-data-overview.md#kusto-query-language-ingest-control-commands)」を参照してください)。                                                                                                                                                                                      | テーブルへのデータの取り込み     |

## <a name="crud-naming-conventions-for-tables"></a>テーブルの CRUD 名前付け規則 
(上記の表のリンク先のセクションの詳細を参照してください)。
 
| コマンド構文                             | Semantics                                                                                                             |
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