---
title: キャッシュ ポリシー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのキャッシュ ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: ca14703b2548bdb23dc3e6e352aeaacbc17303b4
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744472"
---
# <a name="cache-policy"></a>Cache ポリシー

この資料では、[キャッシュ ポリシー](cachepolicy.md)の作成および変更に使用するコマンドについて説明します。 

## <a name="displaying-the-cache-policy"></a>キャッシュ ポリシーの表示

ポリシーは、データまたはテーブルに設定でき、次のいずれかのコマンドを使用して表示されます。

* `.show``database`*データベース名*`policy``caching`
* `.show``table`*データベース名*`.`*テーブル名*`policy``caching`

## <a name="altering-the-cache-policy"></a>キャッシュ ポリシーの変更

```kusto
.alter <entity_type> <database_or_table_name> policy caching hot = <timespan>
```

複数のテーブルのキャッシュ ポリシーを変更する (同じデータベース コンテキスト内):

```kusto
.alter tables (table_name [, ...]) policy caching hot = <timespan>
```

キャッシュ ポリシー:

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

* `entity_type`: テーブル、データベース、またはクラスタ
* `database_or_table`: entity がテーブルまたはデータベースの場合、その名前は次のようにコマンドで指定する必要があります。 
  - `database_name` または 
  - `database_name.table_name` または 
  - `table_name`(特定のデータベースのコンテキストで実行する場合)

## <a name="deleting-the-cache-policy"></a>キャッシュ ポリシーの削除

```kusto
.delete <entity_type> <database_or_table_name> policy caching
```

**使用例**

データベース`MyTable``MyDatabase`のテーブルのキャッシュ ポリシーを表示する :

```kusto
.show table MyDatabase.MyTable policy caching 
```

(データベース コンテキスト内`MyTable`の) テーブルのキャッシュ ポリシーを 3 日間に設定します。

```kusto
.alter table MyTable policy caching hot = 3d
```

複数のテーブルのポリシーを (データベースコンテキストで) 3 日間に設定する:

```kusto
.alter tables (MyTable1, MyTable2, MyTable3) policy caching hot = 3d
```

テーブルのポリシー セットの削除:

```kusto
.delete table MyTable policy caching
```

データベースのポリシー・セットの削除:

```kusto
.delete database MyDatabase policy caching
```
