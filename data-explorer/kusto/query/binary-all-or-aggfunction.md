---
title: binary_all_or () (集計関数)-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの binary_all_or () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: e00d170db7cbafb36f04dfeb14f64caf2b8abcff
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225260"
---
# <a name="binary_all_or-aggregation-function"></a>binary_all_or () (集計関数)

概要作成グループごとにバイナリ演算を使用して値を累積し `OR` ます (または、グループ化せずに概要作成を実行する場合は合計)。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

**構文**

集計の `binary_all_or(` *Expr*`)`

**引数**

* *Expr*: long 数値。

**戻り値**

集計グループごとのレコードに対するバイナリ演算を使用して集計された値を返し `OR` ます (集約がグループ化されていない場合は合計で)。

**例**

バイナリ演算を使用して ' カフェ ' を作成してい `OR` ます:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(num:long)
[
  0x88888008,
  0x42000000,
  0x00767000,
  0x00000005, 
]
| summarize result = toupper(tohex(binary_all_or(num)))
```

|結果|
|---|
|CAFEF00D|
