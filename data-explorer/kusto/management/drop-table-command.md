---
title: . テーブルを削除し、テーブルを削除する-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでテーブルを削除し、テーブルを削除する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 3e1eb57741302d34664f6cd8f256612a6e70bdd1
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264489"
---
# <a name="drop-table-and-drop-tables"></a>. テーブルを削除し、テーブルを削除します。

データベースから1つまたは複数のテーブルを削除します。

[Table admin 権限](../management/access-control/role-based-authorization.md)が必要です。

> [!NOTE]
> このコマンドでは `.drop` `table` 、データのみが論理的に削除されます。 つまり、データを照会することはできませんが、永続ストレージから回復できます。 基になるストレージアーティファクトは、データが `recoverability` テーブルに取り込まれたされたときに有効であった[保持ポリシー](../management/retentionpolicy.md)のプロパティに従って、ハード削除されます。

**構文**

`.drop``table` *TableName* [ `ifexists` ]

`.drop``tables`(*TableName1*、 *TableName2*,..) [ifexists]

> [!NOTE]
> `ifexists`を指定した場合、存在しないテーブルがあると、コマンドは失敗しません。

**例**

```kusto
.drop table CustomersTable ifexists
.drop tables (ProductsTable, ContactsTable, PricesTable) ifexists
```

**戻り値**

このコマンドは、データベース内の残りのテーブルの一覧を返します。

| 出力パラメーター | Type   | 説明                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | テーブルの名前。                  |
| DatabaseName     | String | テーブルが属しているデータベース。 |
