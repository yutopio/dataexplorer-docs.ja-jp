---
title: . create table-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでテーブルを作成する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: 8cfdbe1420745620fcaaf6af81e4f750ca25c1cd
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321609"
---
# <a name="create-table"></a>.create テーブル

新しい空のテーブルを作成します。

コマンドは、特定のデータベースのコンテキストで実行する必要があります。

[データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

**構文**

`.create``table` *TableName* ([columnName: columnType],...) [ `with` `(` [ `docstring` `=` *ドキュメント*] [ `,` `folder` `=` *FolderName*] `)` ]

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
> 複数のテーブルを作成する場合は、クラスターのパフォーマンスを向上させ、負荷を軽減するために、コマンドを使用し [`.create tables`](create-tables-command.md) ます。
