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
ms.openlocfilehash: 2474b8751be5cba2270bcbd2936c76b91f5f3ba0
ms.sourcegitcommit: fbe298e88542c0dcea0f491bb53ac427f850f729
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/24/2020
ms.locfileid: "82030040"
---
# <a name="ingestion_time"></a>ingestion_time()

レコードの非表示`$IngestionTime` `datetime`の列または null を取得します。
この`$IngestionTime`列は、テーブルの

::: zone pivot="azuredataexplorer"

[IngestionTime ポリシー](../management/ingestiontimepolicy.md)が設定されています (有効)。

::: zone-end

::: zone pivot="azuremonitor"

IngestionTime ポリシーが設定されています (有効)。

::: zone-end

テーブルにこのポリシーが定義されていない場合は、null 値が返されます。

この関数は、関連するデータを返すために、実際のテーブルのコンテキストで使用する必要があります。 たとえば、 `summarize`演算子の後で呼び出された場合、すべてのレコードに対して null が返されます。

**構文**

 `ingestion_time()`

**戻り値**

テーブル`datetime`へのインジェストのおおよその時間を指定する値です。

**例**

```kusto
T 
| extend ingestionTime = ingestion_time() | top 10 by ingestionTime
```
