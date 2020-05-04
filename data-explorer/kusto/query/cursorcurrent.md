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
ms.openlocfilehash: 5291fa664dc4736179d7f20984eacfd44efd5888
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737659"
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

この機能は、ではサポートされていません Azure Monitor

::: zone-end
