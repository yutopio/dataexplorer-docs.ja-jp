---
title: . alter table-Azure データエクスプローラー
description: この記事では、. alter table コマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/08/2020
ms.openlocfilehash: ccefca3d3cbf1f97661fead54bbc3cfaf207ca1a
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321677"
---
# <a name="alter-table"></a>.alter テーブル
 
`.alter table` コマンドは、次のことを行います。
* "保存済み" 列のデータを保護する
* テーブル列の並べ替え
* 新しい列スキーマ、 `docstring` 、およびフォルダーを既存のテーブルに設定し、既存の列スキーマ、 `docstring` 、およびフォルダーを上書きします。
* テーブル名をスコープとする特定のデータベースのコンテキストで実行する必要があります。
* [Table Admin 権限](../management/access-control/role-based-authorization.md)が必要です

> [!WARNING]
> コマンドを誤って使用すると、 `.alter` データが失われる可能性があります。

> [!TIP]
> には、 `.alter` 同等の機能を持つテーブルコマンドである対応するがあり `.alter-merge` ます。 詳細については、[`.alter-merge table`](../management/alter-merge-table-command.md) を参照してください。

**構文**

`.alter``table` *TableName* (*columnName*:*columnType*,...) [ `with` `(` [ `docstring` `=` *ドキュメント*] [ `,` `folder` `=` *FolderName*] `)` ]


 * このテーブルには、指定された順序で、まったく同じ列があります。
 テーブルの列を指定します。
 * コマンドで既存の列が指定されていない場合は、削除され、その中のデータはコマンドのように失われ `.drop column` ます。
 * テーブルを変更しても、列の型を変更することはできません。 代わりに、 [`.alter column`](alter-column.md) コマンドを使用してください。

> [!TIP]
> `.show table [TableName] cslschema`既存の列スキーマを変更する前に、を使用して取得します。


コマンドがデータにどのように影響するか。
* 既存のデータは、コマンドによって物理的に変更されることはありません。 削除された列のデータは無視されます。 新しい列のデータは null であると見なされます。
* クラスターの構成方法によっては、ユーザーの操作がなくても、データインジェストによってテーブルの列スキーマが変更されることがあります。 テーブルの列スキーマを変更する場合は、インジェストによって、コマンドが削除する必要のある列が追加されないようにしてください。

> [!WARNING]
> テーブルの列スキーマを変更し、コマンドと並行して実行されるテーブルへのデータインジェスト処理 `.alter table` は、テーブルの列の順序に依存しない場合があります。 また、データが間違った列に取り込まれたされるリスクもあります。 これらの問題を回避するには、コマンドの実行中にインジェストを停止するか、このようなインジェスト操作で常にマッピングオブジェクトが使用されるようにします。

**使用例**

```kusto
.alter table MyTable (ColumnX:string, ColumnY:int) 
.alter table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```
