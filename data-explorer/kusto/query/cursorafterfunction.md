---
title: cursor_after ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの cursor_after () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 9fab1ec936e950368667fc3afb133dcd952e44b5
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737693"
---
# <a name="cursor_after"></a>cursor_after()

::: zone pivot="azuredataexplorer"

テーブルのレコードに対する述語で、データベースカーソルに対するインジェスト時間を比較します。

**構文**

`cursor_after``(` *RHS*`)`

**引数**

* *RHS*: 空の文字列リテラル、または有効なデータベースカーソル値のいずれかです。

**戻り値**

レコードがデータベースカーソル*RHS* (`true`) または not (`false`) の後に取り込まれたされたかどうかを示す、型`bool`のスカラー値。

**メモ**

データベースカーソルの詳細については、「[データベースカーソル](../management/databasecursor.md)」を参照してください。

この関数は、 [IngestionTime ポリシー](../management/ingestiontimepolicy.md)が有効になっているテーブルのレコードに対してのみ呼び出すことができます。

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
