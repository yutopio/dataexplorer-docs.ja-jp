---
title: シャーディング ポリシー管理 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのシャーディング ポリシー管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7770e0834e4b00f42158732e667d41eb636cec5b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520045"
---
# <a name="sharding-policy-management"></a>シャーディングポリシー管理

## <a name="show-policy"></a>ポリシーを表示する

```kusto
.show table [table_name] policy sharding

.show table * policy sharding

.show database [database_name] policy sharding
```

`Show`ポリシーは、データベースまたはテーブルのシャーディング ポリシーを表示します。 指定された名前が 「*」の場合、指定されたエンティティタイプ (データベースまたはテーブル) のすべてのポリシーが表示されます。

### <a name="output"></a>出力

|ポリシー名 | エンティティ名 | ポリシー | 子エンティティ | エンティティの種類
|---|---|---|---|---
|エクステントシャーディングポリシー | データベース/テーブル名 | ポリシーを表す json 形式の文字列 | テーブルのリスト (データベース用)|データベース/テーブル

## <a name="alter-policy"></a>ポリシーの変更

### <a name="examples"></a>例

次の例では、エンティティの更新されたエクステント シャーディング ポリシーを返し、データベースまたはテーブルを修飾名として指定して、その出力として返します。

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>ポリシーのすべてのプロパティをテーブル レベルで明示的に設定する

```kusto
.alter table [table_name] policy sharding 
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-all-properties-of-the-policy-explicitly-at-database-level"></a>ポリシーのすべてのプロパティをデータベース レベルで明示的に設定する

```kusto
.alter database [database_name] policy sharding
@'{ "MaxRowCount": 750000, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048}'
```

#### <a name="setting-the-default-sharding-policy-at-database-level"></a>データベース レベルでの*既定*のシャーディング ポリシーの設定

```kusto
.alter database [database_name] policy sharding @'{}'
```

#### <a name="altering-a-single-property-of-the-policy-at-database-level"></a>データベース レベルでのポリシーの単一のプロパティの変更 

他のすべてのプロパティはそのままにします。

```kusto
.alter-merge database [database_name] policy sharding
@'{ "MaxExtentSizeInMb": 1024}'
```

#### <a name="altering-a-single-property-of-the-policy-at-table-level"></a>テーブル レベルでのポリシーの単一のプロパティの変更

他のすべてのプロパティをそのまま保持

```kusto
.alter-merge table [table_name] policy sharding
@'{ "MaxRowCount": 750000}'
```

## <a name="delete-policy"></a>ポリシーの削除

```kusto
.delete table [table_name] policy sharding

.delete database [database_name] policy sharding
```

このコマンドは、指定されたエンティティの現在のシャーディング ポリシーを削除します。