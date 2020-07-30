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
ms.openlocfilehash: f85c2c45ff4e69ba59f2a13313c8c2ac494c56a6
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340991"
---
# <a name="tdigest_merge"></a>tdigest_merge()

`tdigest`結果のマージ (集計バージョンのスカラーバージョン [`tdigest_merge()`](tdigest-merge-aggfunction.md) )。

基になるアルゴリズム (T ダイジェスト) と推定エラーの詳細については、[こちら](percentiles-aggfunction.md#estimation-error-in-percentiles)を参照してください。

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
