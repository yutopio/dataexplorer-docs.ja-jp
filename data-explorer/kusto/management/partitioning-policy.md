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
ms.openlocfilehash: 4f5abfd5c7fffd126033baeb2bbb9243b4400f58
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967402"
---
# <a name="partitioning-policy-command"></a>パーティション分割ポリシー コマンド

データのパーティション分割ポリシーの詳細については、[こちら](../management/partitioningpolicy.md)を参照してください。

## <a name="show-policy"></a>ポリシーの表示

```kusto
.show table [table_name] policy partitioning
```

コマンドは、 `.show` テーブルに適用されているパーティション分割ポリシーを表示します。

### <a name="output"></a>出力

|ポリシー名 | エンティティ名 | ポリシー | 子エンティティ | エンティティの種類
|---|---|---|---|---
|DataPartitioning | テーブル名 | ポリシーオブジェクトの JSON シリアル化 | null | Table

## <a name="alter-and-alter-merge-policy"></a>alter および alter-merge ポリシー

```kusto
.alter table [table_name] policy partitioning @'policy object, serialized as JSON'

.alter-merge table [table_name] policy partitioning @'partial policy object, serialized as JSON'
```

このコマンドでは、 `.alter` テーブルに適用されているパーティション分割ポリシーを変更できます。

コマンドには[databaseadmin](access-control/role-based-authorization.md)のアクセス許可が必要です。

ポリシーに対する変更は、有効になるまで最大1時間かかる場合があります。

### <a name="examples"></a>例

#### <a name="setting-a-policy-with-a-hash-partition-key"></a>ハッシュパーティションキーを使用してポリシーを設定する

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
    '}'
  ']'
'}'
```

#### <a name="setting-a-policy-with-a-uniform-range-datetime-partition-key"></a>Uniform range datetime パーティションキーを使用してポリシーを設定する

```kusto
.alter table [table_name] policy partitioning @'{'
  '"PartitionKeys": ['
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

#### <a name="setting-a-policy-with-both-kinds-of-partition-keys"></a>両方の種類のパーティションキーを使用してポリシーを設定する

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

ポリシーのを別の値に設定するには、 `EffectiveDateTime` 次のコマンドを使用します。

```kusto
.alter-merge table [table_name] policy partitioning @'{"EffectiveDateTime":"2020-01-01"}'
```

## <a name="delete-policy"></a>ポリシーの削除

```kusto
.delete table [table_name] policy partitioning
```

コマンドは、 `.delete` 指定されたテーブルのパーティション分割ポリシーを削除します。
