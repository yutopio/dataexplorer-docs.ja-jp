---
title: マージ ポリシー管理 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのマージ ポリシー管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4093c9c09e4e268bc38cabdc6da27f0ac2ee17ab
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81664022"
---
# <a name="merge-policy-management"></a>マージ ポリシー管理

## <a name="show-policy"></a>ポリシーを表示する

```kusto
.show table [table_name] policy merge

.show table * policy merge

.show database [database_name] policy merge

.show database * policy merge
```

データベースまたはテーブルの現在のマージ ポリシーを表示します。
指定された名前が "*" の場合、指定されたエンティティの種類 (データベースまたはテーブル) のすべてのポリシーを表示します。

### <a name="output"></a>出力

|ポリシー名 | エンティティ名 | ポリシー | 子エンティティ | エンティティの種類
|---|---|---|---|---
|エクステントマージポリシー | データベース/テーブル名 | ポリシーを表す JSON 形式の文字列 | テーブルのリスト (データベース用)|データベース &#124; テーブル

## <a name="alter-policy"></a>ポリシーの変更

### <a name="examples"></a>例

#### <a name="1-setting-all-properties-of-the-policy-explicitly-at-table-level"></a>1. ポリシーのすべてのプロパティをテーブルレベルで明示的に設定する場合:

```kusto
.alter table [table_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"RowCountUpperBoundForRebuild": 750000, '
    '"RowCountUpperBoundForMerge": 0, '
    '"MaxExtentsToMerge": 100, '
    '"LoopPeriod": "01:00:00", '
    '"MaxRangeInHours": 8, '
    '"AllowRebuild": true,'
    '"AllowMerge": true '
'}'
```

#### <a name="2-setting-all-properties-of-the-policy-explicitly-at-database-level"></a>2. ポリシーのすべてのプロパティをデータベースレベルで明示的に設定する場合:

```kusto
.alter database [database_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"RowCountUpperBoundForRebuild": 750000,'
    '"RowCountUpperBoundForMerge": 0,'
    '"MaxExtentsToMerge": 100,'
    '"LoopPeriod": "01:00:00",'
    '"MaxRangeInHours": 8,'
    '"AllowRebuild": true,'
    '"AllowMerge": true'
'}'
```

#### <a name="3-setting-the-default-merge-policy-at-database-level"></a>3. データベースレベルで*のデフォルト*のマージポリシーの設定

```kusto
.alter database [database_name] policy merge '{}'
```

#### <a name="4-altering-a-single-property-of-the-policy-at-database-level-keeping-all-other-properties-as-is"></a>4. ポリシーの単一のプロパティをデータベース レベルで変更し、その他のすべてのプロパティを維持します。

```kusto
.alter-merge database [database_name] policy merge
@'{'
    '"ExtentSizeTargetInMb": 1024'
'}'
```

#### <a name="5-altering-a-single-property-of-the-policy-at-table-level-keeping-all-other-properties-as-is"></a>5. ポリシーの単一のプロパティをテーブルレベルで変更し、その他のすべてのプロパティを維持します。

```kusto
.alter-merge table [table_name] policy merge
@'{'
    '"RowCountUpperBoundForRebuild": 750000'
'}'
```

上記のすべては、エンティティ (修飾名として指定されたデータベースまたはテーブル) の更新されたエクステント マージ ポリシーを出力として返します。

ポリシーの変更が有効になるには、最大で 1 時間かかる場合があります。

## <a name="delete-policy-of-merge"></a>マージのポリシーを削除する

```kusto
.delete table [table_name] policy merge

.delete database [database_name] policy merge

```

このコマンドは、指定されたエンティティの現在のマージ ポリシーを削除します。
