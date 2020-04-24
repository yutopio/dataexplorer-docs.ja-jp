---
title: dcount_hll() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでdcount_hll() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: a0c921efa90f5d66fe42fa6ee872204b5bb399cd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516152"
---
# <a name="dcount_hll"></a>dcount_hll()

hll の結果から dcount を計算します[(hll](hll-aggfunction.md)または[hll_merge](hll-merge-aggfunction.md)で生成されました)。

[基礎となるアルゴリズム *(H*yper*L*og*L*og) と推定精度](dcount-aggfunction.md#estimation-accuracy)について読んでください。

**構文**

`dcount_hll(`*Expr*`)`

**引数**

* *Expr*: [hll](hll-aggfunction.md)または[hll マージ](hll-merge-aggfunction.md)によって生成された式

**戻り値**

*Expr*の各値の個別のカウント

**使用例**

```kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

|dcount_hll_hllMerged|
|---|
|315|