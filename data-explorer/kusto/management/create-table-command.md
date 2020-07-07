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
ms.openlocfilehash: b071c4af6bc25650d18b1b66130941f73af551ff
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967096"
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
> 複数のテーブルを作成する場合は、[[テーブルの作成](create-tables-command.md)] コマンドを使用して、クラスターのパフォーマンスを向上させ、負荷を軽減します。
