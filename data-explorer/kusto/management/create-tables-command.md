---
title: . テーブルを作成する-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでテーブルを作成する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/05/2020
ms.openlocfilehash: ff8b3cfae6d3364b4d094f588c8130761fa5cb31
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85966994"
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
