---
title: キャッシュポリシー-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのキャッシュポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 319a71e5db7019ed28001f44a1d4a4bcb21984e9
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967249"
---
# <a name="cache-policy-command"></a>キャッシュ ポリシー コマンド

この記事では、[キャッシュポリシー](cachepolicy.md)の作成と変更に使用されるコマンドについて説明します。 

## <a name="displaying-the-cache-policy"></a>キャッシュポリシーを表示しています

ポリシーはデータまたはテーブルに対して設定でき、次のいずれかのコマンドを使用して表示されます。

* `.show``database` *DatabaseName* DatabaseName `policy``caching`
* `.show``table` *DatabaseName* `.` *TableName* TableName `policy``caching`

## <a name="altering-the-cache-policy"></a>キャッシュポリシーの変更

```kusto
.alter <entity_type> <database_or_table_name> policy caching hot = <timespan>
```

複数のテーブルのキャッシュポリシーを変更する (同じデータベースコンテキスト内):

```kusto
.alter tables (table_name [, ...]) policy caching hot = <timespan>
```

キャッシュポリシー:

```kusto
{
  "DataHotSpan": {
    "Value": "3.00:00:00"
  },
  "IndexHotSpan": {
    "Value": "3.00:00:00"
  }
}
```

* `entity_type`: テーブル、データベース、またはクラスター
* `database_or_table`: エンティティがテーブルまたはデータベースの場合は、次のようにコマンドでその名前を指定する必要があります。 
  - `database_name` 
  - `database_name.table_name` 
  - `table_name`(特定のデータベースのコンテキストで実行されている場合)

## <a name="deleting-the-cache-policy"></a>キャッシュポリシーを削除しています

```kusto
.delete <entity_type> <database_or_table_name> policy caching
```

**使用例**

データベースのテーブルのキャッシュポリシーを表示する `MyTable` `MyDatabase` :

```kusto
.show table MyDatabase.MyTable policy caching 
```

テーブルのキャッシュポリシー `MyTable` (データベースコンテキスト) を3日間に設定します。

```kusto
.alter table MyTable policy caching hot = 3d
```

(データベースコンテキストでの) 複数のテーブルのポリシーを、3日間に設定します。

```kusto
.alter tables (MyTable1, MyTable2, MyTable3) policy caching hot = 3d
```

テーブルに設定されるポリシーを削除します。

```kusto
.delete table MyTable policy caching
```

データベースで設定されるポリシーを削除します。

```kusto
.delete database MyDatabase policy caching
```
