---
title: cursor_after() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでcursor_after() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 39ec32322b74b55182522e4bbb04aa0c3830d8d2
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766015"
---
# <a name="cursor_after"></a>cursor_after()

::: zone pivot="azuredataexplorer"

データベース カーソルに対するそれらの取り込み時間を比較するテーブルのレコード上の述語。

**構文**

`cursor_after``(` *RHS*`)`

**引数**

* *RHS*: 空の文字列リテラルか、有効なデータベース カーソル値。

**戻り値**

データベース カーソル*RHS* (`true`) の後にレコードが取り込まれたかどうかを示す型`false``bool`のスカラー値 ( )

**メモ**

[データベース カーソルの詳細については、データベース](../management/databasecursor.md)カーソルを参照してください。

この関数は、[インジェスタのポリシー](../management/ingestiontimepolicy.md)が有効になっているテーブルのレコードに対してのみ呼び出すことができます。

::: zone-end

::: zone pivot="azuremonitor"

これは Azure モニターではサポートされていません。

::: zone-end
