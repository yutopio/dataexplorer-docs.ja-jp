---
title: . alter-merge table-Azure データエクスプローラー
description: この記事では、. alter-merge table コマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/08/2020
ms.openlocfilehash: 1a58d44e7884fb198f04a9f12a71c77cf164331b
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321694"
---
# <a name="alter-merge-table"></a>.alter-merge table
 
`.alter-merge table` コマンドは、次のことを行います。

* 既存の列のデータを保護する
* 既存のテーブルに新しい列、 `docstring` 、およびフォルダーを追加します。
* テーブル名をスコープとする特定のデータベースのコンテキストで実行する必要があります。
* [Table Admin 権限](../management/access-control/role-based-authorization.md)が必要です

> [!WARNING]
> コマンドを誤って使用すると、 `.alter-merge` データが失われる可能性があります。

> [!TIP]
> には、 `.alter-merge` 同等の機能を持つテーブルコマンドである対応するがあり `.alter` ます。 詳細については、[`.alter table`](../management/alter-table-command.md) を参照してください。

**構文**

`.alter-merge``table` *TableName* (*columnName*:*columnType*,...) [ `with` `(` [ `docstring` `=` *ドキュメント*] [ `,` `folder` `=` *FolderName*] `)` ]

テーブルの列を指定します。
 * 存在しない列と指定した列は、既存のスキーマの最後に追加されます。
 * 渡されたスキーマにテーブル列が含まれていない場合、列は削除されません。
 * 別の型の既存の列を指定すると、コマンドは失敗します。

> [!TIP]
> `.show table [TableName] cslschema`既存の列スキーマを変更する前に、を使用して取得します。

コマンドがデータにどのように影響するか。
* 既存のデータは、コマンドによって物理的に変更されることはありません。 削除された列のデータは無視されます。 新しい列のデータは null であると見なされます。
* クラスターの構成方法によっては、ユーザーの操作がなくても、データインジェストによってテーブルの列スキーマが変更されることがあります。 テーブルの列スキーマを変更する場合は、インジェストによって、コマンドが削除する必要のある列が追加されないようにしてください。

**使用例**

```kusto
.alter-merge table MyTable (ColumnX:string, ColumnY:int) 
.alter-merge table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```
 
