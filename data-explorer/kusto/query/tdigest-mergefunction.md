---
title: tdigest_merge ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの tdigest_merge () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 92dce1a98cc0e24dcfbfcd7cb875fa370e3ae1d0
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737727"
---
# <a name="tdigest_merge"></a>tdigest_merge()

結果`tdigest`のマージ (集計バージョン[`tdigest_merge()`](tdigest-merge-aggfunction.md)のスカラーバージョン)。

基になるアルゴリズム (T ダイジェスト) と推定エラーの詳細については、[こちら](percentiles-aggfunction.md#estimation-error-in-percentiles)を参照してください。

**構文**

`merge_tdigests(`*Expr1 or* `,` *Expr2*`, ...)`

`tdigest_merge(`*Expr1 or* `,` *Expr2* Expr2`, ...)` -エイリアス。

**引数**

* マージする`tdigest`値を持つ列。

**戻り値**

列`*Expr1*` `*Expr2*`のマージの結果です。`*ExprN*` 1 つ`tdigest`になります。

**使用例**

```kusto
range x from 1 to 10 step 1 
| extend y = x + 10
| summarize tdigestX = tdigest(x), tdigestY = tdigest(y)
| project merged = tdigest_merge(tdigestX, tdigestY)
| project percentile_tdigest(merged, 100, typeof(long))
```

|percentile_tdigest_merged|
|---|
|20|