---
title: シャーディングポリシー管理-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのシャーディングポリシー管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fb4ff4ade5e3fb0e2f01de0adc74aecd27381a3d
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967470"
---
# <a name="sharding-policy-command"></a>シャーディング ポリシー コマンド

## <a name="show-policy"></a>ポリシーの表示

```kusto
.show table [table_name] policy sharding

.show table * policy sharding

.show database [database_name] policy sharding
```

`Show`ポリシーデータベースまたはテーブルのシャーディングポリシーを表示します。 指定された名前が ' * ' の場合、特定のエンティティ型 (データベースまたはテーブル) のすべてのポリシーが表示されます。

### <a name="output"></a>出力

|ポリシー名 | エンティティ名 | ポリシー | 子エンティティ | エンティティの種類
|---|---|---|---|---
|ExtentsShardingPolicy | データベース/テーブル名 | ポリシーを表す json 形式の文字列 | テーブルの一覧 (データベース用)|データベース/テーブル

## <a name="alter-policy"></a>ポリシーの変更

### <a name="examples"></a>例

次の例では、エンティティに対して更新されたエクステントシャーディングポリシーが返されます。これは、データベースまたはテーブルが修飾名として指定され、出力として指定されます。

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>テーブルレベルでポリシーのすべてのプロパティを明示的に設定する

```kusto
.alter table [table_name] policy sharding 
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-all-properties-of-the-policy-explicitly-at-database-level"></a>データベースレベルでポリシーのすべてのプロパティを明示的に設定する

```kusto
.alter database [database_name] policy sharding
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-the-default-sharding-policy-at-database-level"></a>データベースレベルでの*既定*のシャーディングポリシーの設定

```kusto
.alter database [database_name] policy sharding @'{}'
```

#### <a name="altering-a-single-property-of-the-policy-at-database-level"></a>データベースレベルでのポリシーの1つのプロパティの変更 

その他のプロパティはすべてそのままにしておきます。

```kusto
.alter-merge database [database_name] policy sharding
@'{ "MaxExtentSizeInMb": 1024}'
```

#### <a name="altering-a-single-property-of-the-policy-at-table-level"></a>テーブルレベルでポリシーの1つのプロパティを変更する

その他のプロパティはすべてそのままにしておきます。

```kusto
.alter-merge table [table_name] policy sharding
@'{ "MaxRowCount": 750000}'
```

## <a name="delete-policy"></a>ポリシーの削除

```kusto
.delete table [table_name] policy sharding

.delete database [database_name] policy sharding
```

このコマンドは、指定されたエンティティの現在のシャーディングポリシーを削除します。