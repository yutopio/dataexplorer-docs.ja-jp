---
title: hll_merge ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの hll_merge () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 6bf364106bef8fbe626f96c22502dae748884180
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252960"
---
# <a name="hll_merge"></a>hll_merge()

`hll`結果のマージ (集計バージョンのスカラーバージョン [`hll_merge()`](hll-merge-aggfunction.md) )。

[基になるアルゴリズム (*H*Yper*l*og*l*og) と推定精度](dcount-aggfunction.md#estimation-accuracy)について確認します。

## <a name="syntax"></a>構文

`hll_merge(`*Expr1 or* `,`*Expr2*`, ...)`

## <a name="arguments"></a>引数

* マージする値を持つ列 `hll` 。

## <a name="returns"></a>戻り値

列 `*Exrp1*` ( `*Expr2*` 、...) `*ExprN*` を1つの値に `hll` マージする結果。

## <a name="examples"></a>例

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
