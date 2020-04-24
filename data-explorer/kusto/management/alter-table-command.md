---
title: .alter テーブルと .alter-merge テーブル - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .alter テーブルと .alter-merge テーブルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 516c5c7b85f7c310188fd11ae24e52cb23423143
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522374"
---
# <a name="alter-table-and-alter-merge-table"></a>.alter テーブルと .alter-merge テーブル

この`.alter table`コマンドは、新しい列スキーマ、docstring、およびフォルダーを既存のテーブルに設定し、既存の列スキーマ、docstring、およびフォルダーをオーバーライドします。 コマンドによって「保存」される既存の列のデータは保持されます (したがって、このコマンドは、例えば、表の列の順序変更に使用できます)。

この`.alter-merge table`コマンドは、新しい列、docstring およびフォルダーを既存のテーブルに追加します。
既存の列のデータは保持されます。

どちらのコマンドも、テーブル名をスコープとする特定のデータベースのコンテキストで実行する必要があります。

[テーブルの管理者権限](../management/access-control/role-based-authorization.md)が必要です。

**構文**

`.alter``table`*テーブル名*(*列名*:*列タイプ*, .) [`with` `(``docstring``=`*ドキュメント*]`,` `folder` `=` [`)`*フォルダ名*] ]

`.alter-merge``table`*テーブル名*(*列名*:*列タイプ*, .) [`with` `(``docstring``=`*ドキュメント*]`,` `folder` `=` [`)`*フォルダ名*] ]

正常終了後にテーブルに含める列を指定します。 

> [!WARNING]
> コマンドを`.alter`誤って使用すると、データが失われる可能性があります。
> `.alter-merge`以下の違`.alter`いを注意深く読んでください。

`.alter-merge`:

 * 存在しない列、および指定した列は、既存のスキーマの末尾に追加されます。
 * 渡されたスキーマにテーブル列が含まれていない場合、それらは削除されません。
 * 別の型の既存の列を指定した場合、コマンドは失敗します。

`.alter`のみ (`.alter-merge`ない):

 * テーブルの列は、指定された順序と同じ順序で、まったく同じになります。
 * コマンドで指定されていない既存の列は(`.drop column`のように) 削除され、その中のデータは失われます。
 * テーブルを変更する場合、列の型の変更はサポートされていません。 代わりに[.alter 列コマンドを](alter-column.md)使用してください。

> [!TIP] 
> 既存`.show table [TableName] cslschema`の列スキーマを変更する前に取得するために使用します。 

次のコマンドは、両方のコマンドに適用されます。

1. 既存のデータは、これらのコマンドによって物理的に変更されません。 削除された列のデータは無視されます。 新しい列のデータは null と見なされます。
1. クラスターの構成方法によっては、ユーザーの操作がなくても、データの取り込みがテーブルの列スキーマを変更する場合があります。 したがって、テーブルの列スキーマを変更する場合は、コマンドが削除する必要な列がインジェストによって追加されないようにしてください。

> [!WARNING]
> `.alter table`コマンドと並行して発生するテーブルの列スキーマを変更するテーブルへのデータ取り込みプロセスは、テーブル列の順序に依存しない実行される可能性があります。 データが間違った列に取り込まれるリスクもあります。 このようなコマンドの間に取り込みが停止されるか、またはそのような取り込み操作で常にマッピング オブジェクトが使用されるようにすることで、これを防ぐことができます。

**使用例**

```
.alter table MyTable (ColumnX:string, ColumnY:int) 
.alter table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
.alter-merge table MyTable (ColumnX:string, ColumnY:int) 
.alter-merge table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```