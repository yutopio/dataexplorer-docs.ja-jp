---
title: tdigest_merge() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでtdigest_merge() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 988d7f05791723a823a5850f6865a780477f7bd4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506377"
---
# <a name="tdigest_merge"></a>tdigest_merge()

tdigest の結果 (集計バージョンのスカラー[`tdigest_merge()`](tdigest-merge-aggfunction.md)バージョン ) をマージします。

基礎となるアルゴリズム (T-Digest) と推定誤差の詳細[については、 こちらをご覧ください](percentiles-aggfunction.md#estimation-error-in-percentiles)。

**構文**

`merge_tdigests(`*エクスプル1* `,` *エクスプル2*`, ...)`

`tdigest_merge(`*Expr1* `,` *Expr2* `, ...)` - エイリアス。

**引数**

* マージする消化器を持つ列。

**戻り値**

列`*Expr1*`をマージする結果、 、.. `*Expr2*``*ExprN*` 1つの消化に。

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