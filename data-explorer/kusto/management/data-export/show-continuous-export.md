---
title: 継続的なデータエクスポートの表示-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーで継続的なデータエクスポートのプロパティを表示する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: db7583c055468808ade1166c0bfee90859399d32
ms.sourcegitcommit: c7b16409995087a7ad7a92817516455455ccd2c5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88149489"
---
# <a name="show-continuous-export"></a>連続エクスポートの表示

*ContinuousExportName*の連続エクスポートプロパティを返します。 

## <a name="syntax"></a>構文

`.show` `continuous-export` *ContinuousExportName*

## <a name="properties"></a>Properties

| プロパティ             | Type   | 説明                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 連続エクスポートの名前。 |

`.show` `continuous-exports`

データベース内の連続するすべてのエクスポートを返します。 

## <a name="output"></a>出力

| 出力パラメーター    | Type     | 説明                                                             |
|---------------------|----------|-------------------------------------------------------------------------|
| CursorScopedTables  | String   | 明示的にスコープされた (ファクト) テーブルの一覧 (JSON シリアル化)               |
| ExportProperties    | String   | プロパティのエクスポート (JSON シリアル化)                                     |
| ExportedTo          | DateTime | 正常にエクスポートされた最後の日時 (インジェスト時刻)       |
| ExternalTableName   | String   | 外部テーブルの名前                                              |
| ForcedLatency       | TimeSpan | 強制待機時間 (指定されていない場合は null)                                   |
| IntervalBetweenRuns | TimeSpan | 実行間隔                                                   |
| IsDisabled          | Boolean  | 連続エクスポートが無効になっている場合は True                               |
| IsRunning           | Boolean  | 連続エクスポートが現在実行されている場合は True                      |
| LastRunResult       | String   | 最後の連続エクスポート実行の結果 ( `Completed` または `Failed` ) |
| LastRunTime         | DateTime | 連続エクスポートが最後に実行された時刻 (開始時刻)           |
| 名前                | String   | 連続エクスポートの名前                                           |
| クエリ               | String   | クエリをエクスポートする                                                            |
| StartCursor         | String   | この連続エクスポートの最初の実行の開始点         |

