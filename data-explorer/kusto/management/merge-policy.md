---
title: マージポリシー管理-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのマージポリシーの管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4fd1e9e1c67e03a09bf817dc977ee833d3de789c
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967419"
---
# <a name="merge-policy-command"></a>Merge policy コマンド

## <a name="show-policy"></a>ポリシーの表示

```kusto
.show table [table_name] policy merge

.show table * policy merge

.show database [database_name] policy merge

.show database * policy merge
```

データベースまたはテーブルの現在のマージポリシーを表示します。
指定された名前が ' * ' の場合、特定のエンティティ型 (データベースまたはテーブル) のすべてのポリシーを表示します。

### <a name="output"></a>出力

|ポリシー名 | エンティティ名 | ポリシー | 子エンティティ | エンティティ型
|---|---|---|---|---
|ExtentsMergePolicy | データベース/テーブル名 | ポリシーを表す JSON 形式の文字列 | テーブルの一覧 (データベース用)|データベース &#124; テーブル

## <a name="alter-policy"></a>ポリシーの変更

### <a name="examples"></a>例

#### <a name="1-setting-all-properties-of-the-policy-explicitly-at-table-level"></a>1. テーブルレベルで、ポリシーのすべてのプロパティを明示的に設定します。

```kusto
.alter table [table_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"OriginalSizeInMbUpperBoundForMerge": 4096,'
    '"RowCountUpperBoundForRebuild": 750000,'
    '"RowCountUpperBoundForMerge": 0,'
    '"MaxExtentsToMerge": 100,'
    '"LoopPeriod": "01:00:00",'
    '"MaxRangeInHours": 8,'
    '"AllowRebuild": true,'
    '"AllowMerge": true '
'}'
```

#### <a name="2-setting-all-properties-of-the-policy-explicitly-at-database-level"></a>2. データベースレベルで、ポリシーのすべてのプロパティを明示的に設定します。

```kusto
.alter database [database_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"OriginalSizeInMbUpperBoundForMerge": 4096,'
    '"RowCountUpperBoundForRebuild": 750000,'
    '"RowCountUpperBoundForMerge": 0,'
    '"MaxExtentsToMerge": 100,'
    '"LoopPeriod": "01:00:00",'
    '"MaxRangeInHours": 8,'
    '"AllowRebuild": true,'
    '"AllowMerge": true'
'}'
```

#### <a name="3-setting-the-default-merge-policy-at-database-level"></a>3. データベースレベルで*既定*のマージポリシーを設定する:

```kusto
.alter database [database_name] policy merge '{}'
```

#### <a name="4-altering-a-single-property-of-the-policy-at-database-level-keeping-all-other-properties-as-is"></a>4. データベースレベルでポリシーの1つのプロパティを変更し、その他のすべてのプロパティをそのままにします。

```kusto
.alter-merge database [database_name] policy merge
@'{'
    '"ExtentSizeTargetInMb": 1024'
'}'
```

#### <a name="5-altering-a-single-property-of-the-policy-at-table-level-keeping-all-other-properties-as-is"></a>5. テーブルレベルでポリシーの1つのプロパティを変更し、他のすべてのプロパティをそのままにします。

```kusto
.alter-merge table [table_name] policy merge
@'{'
    '"RowCountUpperBoundForRebuild": 750000'
'}'
```

上記のいずれの場合も、エンティティ (修飾名として指定されたデータベースまたはテーブル) の更新されたエクステントのマージポリシーが出力として返されます。

ポリシーに対する変更は、有効になるまで最大1時間かかる場合があります。

## <a name="delete-policy-of-merge"></a>マージのポリシーを削除する

```kusto
.delete table [table_name] policy merge

.delete database [database_name] policy merge

```

コマンドは、指定されたエンティティの現在のマージポリシーを削除します。
