---
title: . create table-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでテーブルを作成する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: 25554b5485562179d849e846fc5e71c587815e86
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108424"
---
# <a name="create-table"></a>.create テーブル

新しい空のテーブルを作成します。

コマンドは、特定のデータベースのコンテキストで実行する必要があります。

[データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

**構文**

`.create``table` *TableName* ([columnName: columnType],...) [`with` `(`[`docstring` `=` *FolderName* *Documentation*`,`ドキュメント] [ `folder` FolderName] `)`] `=`

テーブルが既に存在する場合、コマンドは success を返します。

**例** 

```kusto
.create table MyLogs ( Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32 ) 
```
 
**出力を返す**

次のように、テーブルのスキーマを JSON 形式で返します。

```kusto
.show table MyLogs schema as json
```

> [!NOTE]
> 複数のテーブルを作成する場合は、[[テーブルの作成](create-tables-command.md)] コマンドを使用して、クラスターのパフォーマンスを向上させ、負荷を軽減します。

## <a name="create-merge-table"></a>. create-merge テーブル

新しいテーブルを作成するか、既存のテーブルを拡張します。 

コマンドは、特定のデータベースのコンテキストで実行する必要があります。 

[データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

**構文**

`.create-merge``table` *TableName* ([columnName: columnType],...) [`with` `(`[`docstring` `=` *FolderName* *Documentation*`,`ドキュメント] [ `folder` FolderName] `)`] `=`

テーブルが存在しない場合、は ". create table" コマンドとまったく同じように機能します。

テーブル T が存在し、". create-merge table T (<columns specification>)" コマンドを送信する場合は、次のようになります。

* 以前に T <columns specification>に存在していなかったの列は、t のスキーマの末尾に追加されます。
* に含ま<columns specification>れていない t の列は、t から削除されません。
* の<columns specification>列が T に存在するが、データ型が異なる場合、コマンドは失敗します。
