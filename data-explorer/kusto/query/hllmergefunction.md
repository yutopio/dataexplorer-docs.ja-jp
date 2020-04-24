---
title: hll_merge() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで hll_merge() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 10e726af4e9dd2e129b526f016a7c37dc0c99d50
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514095"
---
# <a name="hll_merge"></a>hll_merge()

hll の結果 (集約バージョンのスカラーバージョン[`hll_merge()`](hll-merge-aggfunction.md)) をマージします。

[基礎となるアルゴリズム *(H*yper*L*og*L*og) と推定精度](dcount-aggfunction.md#estimation-accuracy)について読んでください。

**構文**

`hll_merge(`*エクスプル1* `,` *エクスプル2*`, ...)`

**引数**

* マージする hll 値を持つ列。

**戻り値**

列`*Exrp1*`をマージする結果、 、.. `*Expr2*``*ExprN*` 1 つの hll 値にします。

**使用例**

```kusto
range x from 1 to 10 step 1 
| extend y = x + 10
| summarize hll_x = hll(x), hll_y = hll(y)
| project merged = hll_merge(hll_x, hll_y)
| project dcount_hll(merged)
```

|dcount_hll_merged|
|---|
|20|