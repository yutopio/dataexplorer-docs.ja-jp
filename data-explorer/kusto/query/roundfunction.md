---
title: ラウンド() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで round() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 05111b6261c14f21d8d08e88a4c070faab32dc4c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510236"
---
# <a name="round"></a>round()

指定した精度の丸めソースを返します。

**構文**

`round(`*ソース*`,` [*精度*]`)`

**引数**

* *ソース*: ラウンドが計算されるソーススカラー。
* *精度*: ソースの桁数を丸めます。(デフォルト値は 0)

**戻り値**

指定された精度に丸めたソース。

丸めは、[`bin()`](binfunction.md)/[`floor()`](floorfunction.md)最初の数値を特定の桁数に丸めるのと、最後の丸め値が指定されたビンサイズの整数倍に丸める(round(2.15,1)は2.2を返し、bin(2.15, 1)は2を返す)
 

**使用例**

```kusto
round(2.15, 1)                   // 2.2
round(2.15) (which is the same as round(2.15, 0))                   // 2
round(-50.55, -2)                   // -100
round(21.5, -1)                   // 20
```