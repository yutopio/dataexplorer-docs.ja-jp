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
ms.openlocfilehash: 2cddd645b0b89b3356adabae26874f93b41f8815
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226552"
---
# <a name="hll_merge"></a>hll_merge()

`hll`結果のマージ (集計バージョンのスカラーバージョン [`hll_merge()`](hll-merge-aggfunction.md) )。

[基になるアルゴリズム (*H*Yper*l*og*l*og) と推定精度](dcount-aggfunction.md#estimation-accuracy)について確認します。

**構文**

`hll_merge(`*Expr1 or* `,`*Expr2*`, ...)`

**引数**

* マージする値を持つ列 `hll` 。

**戻り値**

列 `*Exrp1*` ( `*Expr2*` 、...) `*ExprN*` を1つの値に `hll` マージする結果。

**使用例**

<!-- csl: https://help.kusto.windows.net:443/KustoMonitoringPersistentDatabase -->
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
