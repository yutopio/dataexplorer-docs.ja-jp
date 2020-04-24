---
title: cursor_current ()、current_cursor ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの cursor_current ()、current_cursor () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d9a9526fd846523510e8555c04ff3345d9008348
ms.sourcegitcommit: fbe298e88542c0dcea0f491bb53ac427f850f729
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/24/2020
ms.locfileid: "82030465"
---
# <a name="cursor_current-current_cursor"></a>cursor_current(), current_cursor()

::: zone pivot="azuredataexplorer"

スコープ内のデータベースのカーソルの現在の値を取得します。 (という`cursor_current`名前`current_cursor`とはシノニムです)。

**構文**

`cursor_current()`

**戻り値**

スコープ内のデータベースのカーソル`string`の現在の値をエンコードする、型の単一の値を返します。

**メモ**

データベースカーソルの詳細については、「[データベースカーソル](../management/databasecursor.md)」を参照してください。

::: zone-end

::: zone pivot="azuremonitor"

これは、ではサポートされていません Azure Monitor

::: zone-end
