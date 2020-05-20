---
title: dcount_hll ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの dcount_hll () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 1b1b0c2313f32044a7988e0992c00786885ce2aa
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550301"
---
# <a name="dcount_hll"></a>dcount_hll()

Hll 結果からの dcount ( [hll](hll-aggfunction.md)または[hll_merge](hll-merge-aggfunction.md)によって生成された) を計算します。

[基になるアルゴリズム (*H*Yper*l*og*l*og) と推定精度](dcount-aggfunction.md#estimation-accuracy)について確認します。

**構文**

`dcount_hll(`*With*`)`

**引数**

* *Expr*: [hll](hll-aggfunction.md)または[hll-merge](hll-merge-aggfunction.md)によって生成された式

**戻り値**

*Expr*内の各値の個別のカウント

**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

|dcount_hll_hllMerged|
|---|
|315|
