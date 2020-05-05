---
title: hll_merge ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの hll_merge () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 35dab83a658b714a220c7fbd6ff751627c0741e4
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741781"
---
# <a name="hll_merge"></a>hll_merge()

結果`hll`のマージ (集計バージョン[`hll_merge()`](hll-merge-aggfunction.md)のスカラーバージョン)。

[基になるアルゴリズム (*H*Yper*l*og*l*og) と推定精度](dcount-aggfunction.md#estimation-accuracy)について確認します。

**構文**

`hll_merge(`*Expr1 or* `,` *Expr2*`, ...)`

**引数**

* マージする値`hll`を持つ列。

**戻り値**

列`*Exrp1*` `*Expr2*`のマージの結果です。`*ExprN*` 1 つ`hll`の値にします。

**使用例**

```kusto
range x from 1 to 10 step 1 
| extend y = x + 10
| summarize hll_x = hll(x), hll_y = hll(y)
| project merged = hll_merge(hll_x, hll_y)
| project dcount_hll(merged)
```

|`dcount_hll_merged`|
|---|
|20|