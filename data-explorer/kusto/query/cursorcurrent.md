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
ms.openlocfilehash: 879fe4aac2a6714f3d7ab16a63c69cd8215bbb04
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348600"
---
# <a name="cursor_current-current_cursor"></a>cursor_current(), current_cursor()

::: zone pivot="azuredataexplorer"

スコープ内のデータベースのカーソルの現在の値を取得します。 (という名前 `cursor_current` と `current_cursor` はシノニムです)。

## <a name="syntax"></a>構文

`cursor_current()`

## <a name="returns"></a>戻り値

`string`スコープ内のデータベースのカーソルの現在の値をエンコードする、型の単一の値を返します。

**メモ**

データベースカーソルの詳細については、「[データベースカーソル](../management/databasecursor.md)」を参照してください。

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
