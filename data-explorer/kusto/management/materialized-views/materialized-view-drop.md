---
title: 具体化したビューのドロップ-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの具体化されたビューの削除コマンドについて説明します。
services: data-explorer
author: orspod
ms.author: yifats
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 312b8dbd15f9ee570d1693f7bdbb77b9988d8207
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320615"
---
# <a name="drop-materialized-view"></a>.drop materialized-view 

具体化したビューを削除します。

[データベース管理](../access-control/role-based-authorization.md)者または具体化したビューの管理アクセス許可が必要です。

## <a name="syntax"></a>構文

`.drop``materialized-view` *MaterializedViewName*

## <a name="properties"></a>プロパティ

| プロパティ | 種類| 説明 |
|----------------|-------|-----|
| MaterializedViewName| String| 具体化されたビューの名前。|

## <a name="returns"></a>戻り値

このコマンドは、データベース内の残りの具体化されたビューを返します。これは、具体化された [ビューの表示](materialized-view-show-commands.md#show-materialized-view) コマンドの出力です。

## <a name="example"></a>例

```kusto
.drop materialized-view ViewName
```

## <a name="output"></a>出力

|出力パラメーター |種類 |説明
|---|---|---|
|名前  |String |具体化されたビューの名前。
|SourceTable|String|具体化されたビューのソーステーブル。
|クエリ|String|具体化されたビュークエリ。
|MaterializedTo|DATETIME|ソーステーブルの具体化された ingestion_time () タイムスタンプの最大値。 詳細については、「 [具体化ビューのしくみ](materialized-view-overview.md#how-materialized-views-work)」を参照してください。
|LastRun|DATETIME |具体化が最後に実行された時刻。
|LastRunResult|String|前回の実行の結果。 正常に実行された場合は `Completed` 、それ以外の場合はを返し `Failed` ます。
|IsHealthy|[bool]|`True` ビューが正常と見なされた場合は `False` 。それ以外の場合は。 ビューは、最後の1時間に正常に具体化された (がより大きい) 場合、正常と見なされ `MaterializedTo` `ago(1h)` ます。
|IsEnabled|[bool]|`True` ビューが有効になっている場合は、「具体化された [ビューの無効化または有効化](materialized-view-enable-disable.md)」を参照してください。
|Folder|string|具体化されたビューフォルダー。
|DocString|string|具体化されたビューのドキュメント文字列。
|AutoUpdateSchema|[bool]|ビューで自動更新が有効になっているかどうか。
|EffectiveDateTime|DATETIME|作成時に決定される、ビューの有効な日時 (「」を参照 [`.create materialized-view`](materialized-view-create.md#create-materialized-view) )
