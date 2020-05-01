---
title: 。テーブルの詳細を表示します-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでテーブルの詳細を表示する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 3182c059a3f56b94950009672759388bbff8058d
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616917"
---
# <a name="show-table-details"></a>.show テーブルの詳細
指定したテーブルまたはデータベース内のすべてのテーブルを含むセットを返します。各テーブルのプロパティの詳細な概要が表示されます。

[データベースビューアー権限](../management/access-control/role-based-authorization.md)が必要です。

```kusto
.show table T1 details
.show tables (T1, ..., Tn) details
.show tables details
```

**出力**

| 出力パラメーター           | Type     | 説明                                                                                     |
|----------------------------|----------|-------------------------------------------------------------------------------------------------|
| `TableName`                | String   | テーブルの名前。                                                                          |
| `DatabaseName`             | String   | テーブルが属しているデータベース。                                                         |
| `Folder`                   | String   | テーブルのフォルダー。                                                                             |
| `DocString`                | String   | テーブルを文書化する文字列。                                                                 |
| `TotalExtents`             | Int64    | テーブル内のエクステントの合計数。                                                       |
| `TotalExtentSize`          | Double   | テーブルのエクステント (圧縮サイズ + インデックスサイズ) の合計サイズ。                          |
| `TotalOriginalSize`        | Double   | テーブル内の元のデータの合計サイズ。                                                   |
| `TotalRowCount`            | Int64    | テーブル内の行の合計数。                                                          |
| `HotExtents`               | Int64    | ホットキャッシュに格納されているテーブル内のエクステントの合計数。                              |
| `HotExtentSize`            | Double   | ホットキャッシュに格納されているテーブル内のエクステント (圧縮サイズ + インデックスサイズ) の合計サイズ。 |
| `HotOriginalSize`          | Double   | テーブル内のデータの元の合計サイズ。ホットキャッシュに格納されます。                          |
| `HotRowCount`              | Int64    | ホットキャッシュに格納されているテーブル内の行の合計数。                                 |
| `AuthorizedPrincipals`     | String   | JSON としてシリアル化されたテーブルの承認されたプリンシパル。                                          |
| `RetentionPolicy`          | String   | JSON として`*`シリアル化された、テーブルの有効な保持ポリシー。                                  |
| `CachingPolicy`            | String   | JSON として`*`シリアル化された、テーブルの有効なキャッシュポリシー。                                    |
| `ShardingPolicy`           | String   | テーブルの有効`*`なシャーディングポリシー。66666666666666としてシリアル化されます。                     |
| `MergePolicy`              | String   | JSON として`*`シリアル化された、テーブルの有効なマージポリシー。                                      |
| `StreamingIngestionPolicy` | String   | JSON として`*`シリアル化された、テーブルの有効なストリーミングインジェストポリシー。                        |
| `MinExtentsCreationTime`   | DateTime | テーブル内のエクステントの最小作成時間 (エクステントがない場合は null)。         |
| `MaxExtentsCreationTime`   | DateTime | テーブル内のエクステントの最大作成時間 (エクステントがない場合は null)。         |
| `RowOrderPolicy`           | String   | JSON としてシリアル化された、テーブルの有効な行順序ポリシー。                                     |

`*`*親エンティティ (データベース/クラスターなど) のポリシーを*考慮する。

**出力の例**

| TableName         | DatabaseName | Folder | DocString | TotalExtents | TotalExtentSize | TotalOriginalSize | TotalRowCount | ホットエクステント | HotExtentSize | ホット Originalsize | ホット行数 | AuthorizedPrincipals                                                                                                                                                                               | RetentionPolicy                                                                                                                                       | CachingPolicy                                                                        | シャード Ingpolicy                                                                    | MergePolicy                                                                                                                                             | StreamingIngestionPolicy | MinExtentsCreationTime      | MaxExtentsCreationTime      |
|-------------------|--------------|--------|-----------|--------------|-----------------|-------------------|---------------|------------|---------------|-----------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------|-----------------------------|-----------------------------|
| 操作        | 操作   |        |           | 1164         | 37687203        | 53451358          | 223325        | 29         | 838752        | 1388213         | 5117        | [{"Type": "AAD User", "DisplayName": "a7a77777-4c21-4649-95c5-350bf486087b", " alias@fabrikam.com", "aaduser = a7a77777-4c21-4649-95c5-350bf486087b", "note": ""}] "という形式で、[{" Type ":" AAD User "," DisplayName ":" "," ":" "}] | {"SoftDeletePeriod": "365.00:00:00"、"ContainerRecyclingPeriod": "1.00:00:00"、"ExtentsDataSizeLimitInBytes": 0、"OriginalDataSizeLimitInBytes": 0}  | {"Dataホットスパン": "4.00:00:00"、"Indexホットスパン": "4.00:00:00"、"dataview": []} | {"MaxRowCount": 75万, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048} | {"RowCountUpperBoundForMerge": 0、"MaxExtentsToMerge": 100、"LoopPeriod": "01:00:00"、"MaxRangeInHours": 3、"AllowRebuild": true、"Allowrebuild": true} | null                     |
| ServiceOperations | 操作   |        |           | 1109         | 76588803        | 91553069          | 110125        | 27         | 2635742       | 2929926         | 3162        | [{"Type": "AAD User", "DisplayName": "a7a77777-4c21-4649-95c5-350bf486087b", " alias@fabrikam.com", "aaduser = a7a77777-4c21-4649-95c5-350bf486087b", "note": ""}] "という形式で、[{" Type ":" AAD User "," DisplayName ":" "," ":" "}] | {"SoftDeletePeriod": "365.00:00:00"、"ContainerRecyclingPeriod": "1.00:00:00"、"ExtentsDataSizeLimitInBytes": 0、"OriginalDataSizeLimitInBytes": 0} | {"Dataホットスパン": "4.00:00:00"、"Indexホットスパン": "4.00:00:00"、"dataview": []} | {"MaxRowCount": 75万, "MaxExtentSizeInMb": 1024, "MaxOriginalSizeInMb": 2048} | {"RowCountUpperBoundForMerge": 0、"MaxExtentsToMerge": 100、"LoopPeriod": "01:00:00"、"MaxRangeInHours": 3、"AllowRebuild": true、"Allowrebuild": true} | null                     | 2018-02-08 15:30: 38.8489786 | 2018-02-14 07:47: 28.7660267 |
