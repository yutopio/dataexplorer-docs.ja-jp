---
title: . テーブルを作成する-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでテーブルを作成する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/05/2020
ms.openlocfilehash: 28ee7e462245497dd14d3a14a1c0703cc71a8933
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321592"
---
# <a name="create-tables"></a>.create テーブル

新しい空のテーブルを一括操作として作成します。

コマンドは、特定のデータベースのコンテキストで実行する必要があります。

[データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。

**構文**

`.create``tables` *TableName1* ([columnname: columnType],...) [ `,` *TableName2* ([columnname: columnType],...)...] [ `with` `(` `folder` `=` *FolderName*] `)` ]

テーブルが既に存在する場合、コマンドは success を返します。
 
**例** 

```kusto
.create tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```
