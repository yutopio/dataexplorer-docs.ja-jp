---
title: .drop テーブルと .drop テーブル - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの .drop テーブルと .drop テーブルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 5f3a488aba5a6785ceb6ad4a093c520ec0509e5e
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744744"
---
# <a name="drop-table-and-drop-tables"></a>.drop テーブルと .drop テーブル

データベースから 1 つまたは複数のテーブルを削除します。

[テーブル管理者のアクセス許可](../management/access-control/role-based-authorization.md)が必要です。

> [!NOTE]
> コマンド`.drop``table`はデータをソフト削除するだけです (つまり、データは照会できませんが、永続ストレージから回復可能です)。 基になるストレージ アーティファクトは、データがテーブルに取`recoverability`り込まれた時点で有効であった[保持ポリシー](../management/retentionpolicy.md)のプロパティに従って、ハード削除されます。

**構文**

`.drop``table`*テーブル名*`ifexists`[ ]

`.drop``tables` (*テーブル名 1*,*テーブル名 2*,..)[存在する]

> [!NOTE]
> 指定`ifexists`した場合、存在しないテーブルがある場合、コマンドは失敗しません。

**例**

```kusto
.drop table CustomersTable ifexists
.drop tables (ProductsTable, ContactsTable, PricesTable) ifexists
```

**戻り値**

このコマンドは、データベース内の残りのテーブルのリストを返します。 

| 出力パラメーター | Type   | 説明                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | テーブルの名前。                  |
| DatabaseName     | String | テーブルが属するデータベース。 |