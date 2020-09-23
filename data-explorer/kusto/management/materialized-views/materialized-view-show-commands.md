---
title: 具体化されるビューコマンドの表示-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの具体化されたビューコマンドの表示について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 4a9b42410c7a64a54ced0dc326b33242b4b11870
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057205"
---
# <a name="show-materialized-views-commands"></a>。具体化されたビューのコマンドを表示します

次の show コマンドは、具体化された [ビュー](materialized-view-overview.md)に関する情報を表示します。

## <a name="show-materialized-view"></a>。具体化されたビューを表示します

具体化されたビューの定義とその現在の状態に関する情報を表示します。

### <a name="syntax"></a>構文

`.show``materialized-view` *MaterializedViewName*

`.show` `materialized-views`

### <a name="properties"></a>Properties

|プロパティ|種類|説明
|----------------|-------|---|
|MaterializedViewName|String|具体化されたビューの名前。|

### <a name="example"></a>例

```kusto
.show materialized-view ViewName
```

### <a name="output"></a>出力

|出力パラメーター |種類 |説明
|---|---|---
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
|EffectiveDateTime|DATETIME|作成時に決定される、ビューの有効な日時 (「 [具体化されたビューを作成する](materialized-view-create.md#create-materialized-view)」を参照)。

## <a name="show-materialized-view-schema"></a>。具体化されたビュースキーマを表示します

CSL/JSON の具体化されたビューのスキーマを返します。

### <a name="syntax"></a>構文

`.show``materialized-view` *MaterializedViewName*`cslschema`

`.show``materialized-view` *MaterializedViewName* `schema` MaterializedViewName `as``json`

`.show``materialized-view` *MaterializedViewName* `schema` MaterializedViewName `as``csl`

### <a name="output-parameters"></a>出力パラメーター

| 出力パラメーター | 種類   | 説明                                               |
|------------------|--------|-----------------------------------------------------------|
| TableName        | String | 具体化されたビューの名前。                        |
| スキーマ           | 文字列 | 具体化されたビューの csl スキーマ                          |
| DatabaseName     | String | 具体化されたビューが属しているデータベース       |
| Folder           | String | 具体化されるビューのフォルダー                                |
| DocString        | String | 具体化されるビューの docstring                             |

## <a name="show-materialized-view-extents"></a>。具体化されたビューのエクステントを表示します

具体化されたビューの *具体化* された部分に含まれるエクステントを返します。 *具体化*された部分の定義については、「具体化された[ビューのしくみ](materialized-view-overview.md#how-materialized-views-work)」を参照してください。

このコマンドでは、[ [テーブルエクステントの表示](../show-extents.md#table-level) ] コマンドと同じ詳細が提供されます。

### <a name="syntax"></a>構文

`.show``materialized-view` *MaterializedViewName* `extents` [ `hot` ]
 
## <a name="show-materialized-view-failures"></a>。具体化されたビューのエラーを表示します

具体化されたビューの具体化プロセスの一部として発生したエラーを返します。

### <a name="syntax"></a>構文

`.show``materialized-view` *MaterializedViewName*`failures`

### <a name="properties"></a>Properties

|プロパティ|種類|説明
|----------------|-------|---|
|MaterializedViewName|String|具体化されたビューの名前。|

### <a name="output"></a>出力

|出力パラメーター |種類 |説明
|---|---|---
|名前  |Timestamp |失敗のタイムスタンプ。
|OperationId  |String |失敗した実行の操作 ID。
|名前|String|具体化されたビューの名前。
|LastSuccessRun|DATETIME|正常に完了した前回の実行のタイムスタンプ。
|FailureKind|String|エラーの種類。
|詳細|String|エラーの詳細。

