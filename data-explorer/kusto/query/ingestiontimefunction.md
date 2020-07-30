---
title: ingestion_time ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの ingestion_time () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: f40a592521082667815fe3ff38843a2376bda0aa
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347410"
---
# <a name="ingestion_time"></a>ingestion_time()

::: zone pivot="azuredataexplorer"

現在のレコードが取り込まれたされたおおよその時間を返します。

この関数は、データが取り込まれたされたときに[IngestionTime ポリシー](../management/ingestiontimepolicy.md)が有効にされた取り込まれたデータのテーブルのコンテキストで使用する必要があります。 それ以外の場合、この関数は null 値を生成します。

::: zone-end

::: zone pivot="azuremonitor"

`datetime`レコードが取り込まれたされ、クエリの準備が整ったときに、を取得します。

::: zone-end

> [!NOTE]
> この関数によって返される値は、インジェスト処理が完了するまでに数分かかる場合があり、複数のインジェストアクティビティが同時に実行される場合があるため、概数です。 完全に1回保証されるテーブルのすべてのレコードを処理するには、[データベースカーソル](../management/databasecursor.md)を使用します。

## <a name="syntax"></a>構文

`ingestion_time()`

## <a name="returns"></a>戻り値

`datetime`テーブルへのインジェストのおおよその時間を指定する値です。

## <a name="example"></a>例

```kusto
T
| extend ingestionTime = ingestion_time() | top 10 by ingestionTime
```
