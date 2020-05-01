---
title: . alter table と alter-merge table-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの alter table と alter-merge テーブルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 18b0502754e95ca56d6a7f6946b9330bd42b174a
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616441"
---
# <a name="alter-table-and-alter-merge-table"></a>. alter table と. alter-merge テーブル

この`.alter table`コマンドは、新しい列スキーマ、docstring、およびフォルダーを既存のテーブルに設定し、既存の列スキーマ、docstring、およびフォルダーを上書きします。 コマンドによって "保持" されている既存の列のデータは保持されます (したがって、このコマンドを使用すると、テーブルの列を並べ替えることができます)。

`.alter-merge table`コマンドを実行すると、新しい列 docstring とフォルダーが既存のテーブルに追加されます。
既存の列のデータは保持されます。

どちらのコマンドも、テーブル名をスコープとする特定のデータベースのコンテキストで実行する必要があります。

[Table Admin 権限](../management/access-control/role-based-authorization.md)が必要です。

**構文**

`.alter``table` *TableName* (*columnName*:*columnType*,...) [`with` `(`[`docstring` `=` *FolderName* *Documentation*`,`ドキュメント] [ `folder` FolderName] `)`] `=`

`.alter-merge``table` *TableName* (*columnName*:*columnType*,...) [`with` `(`[`docstring` `=` *FolderName* *Documentation*`,`ドキュメント] [ `folder` FolderName] `)`] `=`

正常に完了した後にテーブルが持つ列を指定します。 

> [!WARNING]
> コマンドを`.alter`誤って使用すると、データが失われる可能性があります。
> 以下の相違点`.alter`を`.alter-merge`よくお読みください。

`.alter-merge`:

 * 存在しない列と指定した列は、既存のスキーマの最後に追加されます。
 * 渡されたスキーマにテーブル列が含まれていない場合は、削除されません。
 * 別の型の既存の列を指定した場合、コマンドは失敗します。

`.alter`のみ (not `.alter-merge`):

 * このテーブルには、指定された順序で、まったく同じ列があります。
 * コマンドで指定されていない既存の列は (のよう`.drop column`に) 削除され、それらの列のデータは失われます。
 * テーブルを変更しても、列の型を変更することはできません。 代わりに、 [alter column](alter-column.md)コマンドを使用してください。

> [!TIP] 
> 既存`.show table [TableName] cslschema`の列スキーマを変更する前に、を使用して取得します。 

次のコマンドは、両方のコマンドに適用されます。

1. 既存のデータは、これらのコマンドによって物理的に変更されることはありません。 削除された列のデータは無視されます。 新しい列のデータは null であると見なされます。
1. クラスターの構成方法によっては、ユーザーの操作がなくても、データインジェストによってテーブルの列スキーマが変更されることがあります。 したがって、テーブルの列スキーマを変更する場合は、コマンドが削除する必要のある列がインジェストによって追加されないようにしてください。

> [!WARNING]
> テーブルの列スキーマを変更するテーブルへのデータインジェストプロセスは、 `.alter table`コマンドと並行して実行されますが、テーブル列の順序に依存しない場合があります。 また、データが間違った列に取り込まれたされるリスクもあります。 このような操作を防ぐには、コマンドの実行中にインジェストを停止するか、このようなインジェスト操作が常にマッピングオブジェクトを使用するようにします。

**使用例**

```kusto
.alter table MyTable (ColumnX:string, ColumnY:int) 
.alter table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
.alter-merge table MyTable (ColumnX:string, ColumnY:int) 
.alter-merge table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```