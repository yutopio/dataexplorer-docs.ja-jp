---
title: cursor_current ()、current_cursor ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの cursor_current ()、current_cursor () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 1b2e6f868d5117cbdd3db6ba88c6b77e5f0e8ccf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252446"
---
# <a name="cursor_current-current_cursor"></a>cursor_current(), current_cursor()

::: zone pivot="azuredataexplorer"

スコープ内のデータベースのカーソルの現在の値を取得します。 (という名前 `cursor_current` と `current_cursor` はシノニムです)。

## <a name="syntax"></a>構文

`cursor_current()`

## <a name="returns"></a>戻り値

`string`スコープ内のデータベースのカーソルの現在の値をエンコードする、型の単一の値を返します。

**ノート**

データベースカーソルの詳細については、「 [データベースカーソル](../management/databasecursor.md) 」を参照してください。

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
