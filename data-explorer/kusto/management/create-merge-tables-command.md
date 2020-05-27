---
title: . create-merge tables-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのテーブルの作成と結合のコマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 2f80ea54ece66440dc7a6b40d9d571f04bd3e26b
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011518"
---
# <a name="create-merge-tables"></a>. create-merge テーブル

では、特定のデータベースのコンテキストで、1回の一括操作で既存のテーブルのスキーマを作成および拡張できます。

> [!NOTE]
> [データベースユーザー権限](../management/access-control/role-based-authorization.md)が必要です。
> 既存のテーブルを拡張するには、 [table admin 権限](../management/access-control/role-based-authorization.md)が必要です。

**構文**

`.create-merge``tables` *TableName1* ([columnname: columnType],...) [ `,` *TableName2* ([columnname: columnType],...)...]

* 存在しない、指定されたテーブルが作成されます。
* 既存のテーブルが既に存在する場合は、スキーマが拡張されます。
    * 存在しない列は、既存のテーブルのスキーマの_最後_に追加されます。
    * コマンドで指定されていない既存の列は、既存のテーブルのスキーマからは削除されません。
    * 既存のテーブルのスキーマとは異なるデータ型で指定された既存の列がある場合は、エラーが発生します。 テーブルは作成または拡張されません。

**例**

```kusto
.create-merge tables 
  MyLogs (Level:string, Timestamp:datetime, UserId:string, TraceId:string, Message:string, ProcessId:int32),
  MyUsers (UserId:string, Name:string)
```

**出力を返す**

| TableName | DatabaseName  | フォルダー | DocString |
|-----------|---------------|--------|-----------|
| Mylogs)    | TopComparison |        |           |
| MyUsers   | TopComparison |        |           |
