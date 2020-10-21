---
title: tdigest_merge ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの tdigest_merge () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 4b7c143d29b8a2f446f4929098e9fac4e6166796
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250626"
---
# <a name="tdigest_merge"></a>tdigest_merge()

`tdigest`結果のマージ (集計バージョンのスカラーバージョン [`tdigest_merge()`](tdigest-merge-aggfunction.md) )。

基になるアルゴリズム (T ダイジェスト) と推定エラーの詳細については、 [こちら](percentiles-aggfunction.md#estimation-error-in-percentiles)を参照してください。

## <a name="syntax"></a>構文

`merge_tdigests(`*Expr1 or* `,`*Expr2*`, ...)`

`tdigest_merge(`*Expr1 or* `,`*Expr2* `, ...)`-エイリアス。

## <a name="arguments"></a>引数

* マージする値を持つ列 `tdigest` 。

## <a name="returns"></a>戻り値

列 `*Expr1*` ( `*Expr2*` 、...) を `*ExprN*` 1 つにマージし `tdigest` た結果。

## <a name="examples"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
