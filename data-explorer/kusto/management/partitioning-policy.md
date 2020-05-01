---
title: データのパーティション分割ポリシーの管理-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのデータのパーティション分割ポリシーの管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/04/2020
ms.openlocfilehash: 1ad9b359422b51084f1be1c64d27d656313d9296
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616322"
---
# <a name="data-partitioning-policy-management"></a>データのパーティション分割ポリシーの管理

データのパーティション分割ポリシーの詳細については、[こちら](../management/partitioningpolicy.md)を参照してください。

## <a name="show-policy"></a>ポリシーの表示

```kusto
.show table [table_name] policy partitioning
```

コマンド`.show`は、テーブルに適用されているパーティション分割ポリシーを表示します。

### <a name="output"></a>出力

|ポリシー名 | エンティティ名 | ポリシー | 子エンティティ | エンティティの種類
|---|---|---|---|---
|DataPartitioning | テーブル名 | ポリシーオブジェクトの JSON シリアル化 | null | テーブル

## <a name="alter-and-alter-merge-policy"></a>alter および alter-merge ポリシー

```kusto
.alter table [table_name] policy partitioning @'policy object, serialized as JSON'

.alter-merge table [table_name] policy partitioning @'partial policy object, serialized as JSON'
```

この`.alter`コマンドでは、テーブルに適用されているパーティション分割ポリシーを変更できます。

コマンドには[databaseadmin](access-control/role-based-authorization.md)のアクセス許可が必要です。

ポリシーに対する変更は、有効になるまで最大1時間かかる場合があります。

### <a name="examples"></a>例

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>テーブルレベルでポリシーのすべてのプロパティを明示的に設定する

```kusto
.alter table [table_name] policy partitioning @'{'
  '"PartitionKeys": ['
    '{'
      '"ColumnName": "my_string_column",'
      '"Kind": "Hash",'
      '"Properties": {'
        '"Function": "XxHash64",'
        '"MaxPartitionCount": 256,'
      '}'
    '},'
    '{'
      '"ColumnName": "my_datetime_column",'
      '"Kind": "UniformRange",'
      '"Properties": {'
        '"Reference": "1970-01-01T00:00:00",'
        '"RangeSize": "1.00:00:00"'
      '}'
    '}'
  ']'
'}'
```

#### <a name="setting-a-specific-property-of-the-policy-explicitly-at-table-level"></a>テーブルレベルでポリシーの特定のプロパティを明示的に設定する

ポリシー `EffectiveDateTime`のを別の値に設定するには、次のコマンドを使用します。

```kusto
.alter-merge table [table_name] policy partitioning @'{"EffectiveDateTime":"2020-01-01"}'
```

## <a name="delete-policy"></a>ポリシーの削除

```kusto
.delete table [table_name] policy partitioning
```

コマンド`.delete`は、指定されたテーブルのパーティション分割ポリシーを削除します。
