---
title: cursor_before_or_at() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでcursor_before_or_at() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4d1752c69a6f3515b94c4050cef8f518ff58a235
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765974"
---
# <a name="cursor_before_or_at"></a>cursor_before_or_at()

::: zone pivot="azuredataexplorer"

データベース カーソルに対するそれらの取り込み時間を比較するテーブルのレコード上の述語。

**構文**

`cursor_before_or_at``(` *RHS*`)`

**引数**

* *RHS*: 空の文字列リテラルか、有効なデータベース カーソル値。

**戻り値**

レコードがデータベース・カーソル`bool`*RHS* ( )`true`の前に取り込まれたか、 ( ) されていない`false`かを示す、タイプのスカラー値。

**メモ**

[データベース カーソルの詳細については、データベース](../management/databasecursor.md)カーソルを参照してください。

この関数は、[インジェスタのポリシー](../management/ingestiontimepolicy.md)が有効になっているテーブルのレコードに対してのみ呼び出すことができます。

::: zone-end

::: zone pivot="azuremonitor"

これは Azure モニターではサポートされていません。

::: zone-end
