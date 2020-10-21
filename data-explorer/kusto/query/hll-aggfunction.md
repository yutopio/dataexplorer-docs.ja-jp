---
title: hll () (集計関数)-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの hll () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/15/2020
ms.openlocfilehash: 4474299c804e1b54d3060d639d171652e770d989
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241612"
---
# <a name="hll-aggregation-function"></a>hll () (集計関数)

グループ全体のの中間結果を計算し [`dcount`](dcount-aggfunction.md) ます。集計内の集計のコンテキストでのみ実行 [され](summarizeoperator.md)ます。

[基になるアルゴリズム (*H*Yper*l*og*l*og) と推定精度](dcount-aggfunction.md#estimation-accuracy)について確認します。

## <a name="syntax"></a>構文

`summarize hll(`*`Expr`* `[,` *`Accuracy`*`])`

## <a name="arguments"></a>引数

* *`Expr`*: 集計計算に使用される式。 
* *`Accuracy`* 指定されている場合、速度と精度のバランスを制御します。

  |精度の値 |精度  |速度  |エラー  |
  |---------|---------|---------|---------|
  |`0` | lowest | な | 1.6% |
  |`1` | default  | 均衡 | 0.8% |
  |`2` | high | slow | 0.4%  |
  |`3` | high | slow | 0.28% |
  |`4` | 超高 | 時間 | 0.2% |
    
## <a name="returns"></a>戻り値

グループ全体の個別のカウントの中間結果 *`Expr`* 。
 
**ヒント**

1. 集計関数を使用して、複数 [`hll_merge`](hll-merge-aggfunction.md) の中間結果をマージすることができ `hll` ます (出力のみで機能し `hll` ます)。

1. 関数を使用すると、集計関数からを計算することができ [`dcount_hll`](dcount-hllfunction.md) `dcount` `hll`  /  `hll_merge` ます。

## <a name="examples"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize hll(DamageProperty) by bin(StartTime,10m)

```

|StartTime|`hll_DamageProperty`|
|---|---|
|2007-09-18 20:00: 00.0000000|[[1024, 14], [-5473486921211236216,-6230876016761372746, 3953448761157777955, 4246796580750024372], []]|
|2007-09-20 21:50: 00.0000000|[[1024, 14], [4835649640695509390], []]|
|2007-09-29 08:10: 00.0000000|[[1024, 14], [4246796580750024372], []]|
|2007-12-30 16:00: 00.0000000|[[1024, 14], [4246796580750024372,-8936707700542868125], []]|
