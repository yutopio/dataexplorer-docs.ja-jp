---
title: cursor_after ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの cursor_after () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 3b1a89ab84b62241058a24573c0362f7215f82c7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252468"
---
# <a name="cursor_after"></a>cursor_after()

::: zone pivot="azuredataexplorer"

テーブルのレコードに対する述語で、データベースカーソルに対するインジェスト時間を比較します。

## <a name="syntax"></a>構文

`cursor_after``(` *RHS*`)`

## <a name="arguments"></a>引数

* *RHS*: 空の文字列リテラル、または有効なデータベースカーソル値のいずれかです。

## <a name="returns"></a>戻り値

`bool`レコードがデータベースカーソル*RHS* ( `true` ) または not () の後に取り込まれたされたかどうかを示す、型のスカラー値 `false` 。

**ノート**

データベースカーソルの詳細については、「 [データベースカーソル](../management/databasecursor.md) 」を参照してください。

この関数は、 [IngestionTime ポリシー](../management/ingestiontimepolicy.md) が有効になっているテーブルのレコードに対してのみ呼び出すことができます。

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
