---
title: データ パーティション ポリシー管理 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのデータ パーティションポリシーの管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/04/2020
ms.openlocfilehash: 0e1ff783195f26adf7f98e511ca155f43609098c
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663964"
---
# <a name="data-partitioning-policy-management"></a>データ・パーティション・ポリシー管理

データのパーティション分割ポリシーについては[、ここで詳しく説明](../management/partitioningpolicy.md)します。

## <a name="show-policy"></a>ポリシーを表示する

```kusto
.show table [table_name] policy partitioning
```

この`.show`コマンドは、テーブルに適用されているパーティション化ポリシーを表示します。

### <a name="output"></a>出力

|ポリシー名 | エンティティ名 | ポリシー | 子エンティティ | エンティティの種類
|---|---|---|---|---
|データのパーティション分割 | テーブル名 | ポリシー オブジェクトの JSON シリアル化 | null | テーブル

## <a name="alter-and-alter-merge-policy"></a>変更および変更マージポリシー

```kusto
.alter table [table_name] policy partitioning @'policy object, serialized as JSON'

.alter-merge table [table_name] policy partitioning @'partial policy object, serialized as JSON'
```

この`.alter`コマンドを使用すると、テーブルに適用されるパーティション・ポリシーを変更できます。

コマンドには[データベース管理者の](access-control/role-based-authorization.md)アクセス許可が必要です。

ポリシーの変更が有効になるには、最大で 1 時間かかる場合があります。

### <a name="examples"></a>例

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>ポリシーのすべてのプロパティをテーブル レベルで明示的に設定する

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

#### <a name="setting-a-specific-property-of-the-policy-explicitly-at-table-level"></a>ポリシーの特定のプロパティをテーブル レベルで明示的に設定する

ポリシーの`EffectiveDateTime`を別の値に設定するには、次のコマンドを使用します。

```kusto
.alter table [table_name] policy partitioning @'{"EffectiveDateTime":"2020-01-01"}'
```

## <a name="delete-policy"></a>ポリシーの削除

```kusto
.delete table [table_name] policy partitioning
```

この`.delete`コマンドは、指定された表のパーティション・ポリシーを削除します。
