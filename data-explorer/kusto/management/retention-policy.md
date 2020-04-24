---
title: アイテム保持ポリシー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのアイテム保持ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: b0366bef619d815bbe58f91730eff70ec847c239
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520351"
---
# <a name="retention-policy"></a>保持ポリシー

この記事では、[保持ポリシー](retentionpolicy.md)の作成および変更に使用する制御コマンドについて説明します。

## <a name="show-retention-policy"></a>保持ポリシーの表示

```kusto
.show <entity_type> <database_or_table> policy retention

.show <entity_type> *  policy retention
```

* `entity_type`: テーブルまたはデータベース
* `database_or_table`または`database_name` `database_name.table_name` 、`table_name`または (データベース コンテキスト内)

**例**

という名前`MyDatabase`のデータベースの保持ポリシーを表示します。

```kusto
.show database MyDatabase policy retention
```

## <a name="delete-retention-policy"></a>アイテム保持ポリシーの削除

データ保持ポリシーを削除すると、データ保持期間の制限が無効になります。

テーブルのデータ保持ポリシーを削除すると、テーブルはデータベース レベルから保持ポリシーを派生させます。

```kusto
.delete <entity_type> <database_or_table> policy retention
```

* `entity_type`: テーブルまたはデータベース
* `database_or_table`または`database_name` `database_name.table_name` 、`table_name`または (データベース コンテキスト内)

**例**

という名前`MyTable1`のテーブルの保持ポリシーを削除します。

```kusto
.delete table MyTable policy retention
```


## <a name="alter-retention-policy"></a>保持ポリシーの変更

```kusto
.alter <entity_type> <database_or_table> policy retention <retention_policy>

.alter tables (<table_name> [, ...]) policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table> policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table_name> policy retention [softdelete = <timespan>] [recoverability = disabled|enabled]
```

* `entity_type`: テーブルまたはデータベース
* `database_or_table`または`database_name` `database_name.table_name` 、`table_name`または (データベース コンテキスト内)
* `table_name`: データベース コンテキスト内のテーブルの名前。  ワイルドカード (`*`ここでは使用可) を指定します。
* `retention_policy` :

```
    "{ 
        \"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Disabled\"
    }" 
```

**使用例**

という名前`MyDatabase`のデータベースの保持ポリシーを表示します。

```kusto
.show database MyDatabase policy retention
```

10 日間のソフト削除期間と無効なデータの回復可能性を持つ保持ポリシーを設定します。

```kusto
.alter-merge table Table1 policy retention softdelete = 10d recoverability = disabled
```

10 日間のソフト削除期間と有効なデータの回復可能性を持つ保持ポリシーを設定します。

```kusto
.alter table Table1 policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

上記と同じ保持ポリシーを設定しますが、今回は複数のテーブル (表 1、表 2、および Table3) に対して次のように設定します。

```kusto
.alter tables (Table1, Table2, Table3) policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

デフォルト値 (ソフト・削除期間として 100 年、回復可能性が有効) で保持ポリシーを設定します。

```kusto
.alter table Table1 policy retention "{}"
```