---
title: . テーブルを表示する-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでテーブルを表示する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 3da4f705d3182c52d06c7767a12d9be15a219e5c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618328"
---
# <a name="show-tables"></a>。テーブルを表示する

指定されたテーブルまたはデータベース内のすべてのテーブルを含むセットを返します。

[データベースビューアー権限](../management/access-control/role-based-authorization.md)が必要です。

```kusto
.show tables
.show tables (T1, ..., Tn)
```

**出力**

|出力パラメーター |Type |説明
|---|---|---
|TableName  |String |テーブルの名前。
|DatabaseName  |String |テーブルが属しているデータベース。
|Folder |String |テーブルのフォルダー。
|DocString |String |テーブルを文書化する文字列。

**出力の例**

|テーブル名 |データベース名 |Folder | DocString
|---|---|---|---
|Table1 |DB1 |ログ |サービスログを含む
|Table2 |DB1 | レポート |
|Table3 |DB1 | | 拡張情報 |
|Table4 |DB2 | メトリック| サービスのパフォーマンス情報を含みます
