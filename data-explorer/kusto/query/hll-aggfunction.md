---
title: hll() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで hll() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/15/2020
ms.openlocfilehash: 52eac2984ed29bf8de21fb378a84b789015aa1c5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514129"
---
# <a name="hll-aggregation-function"></a>hll() (集計関数)

グループ全体の[dcount](dcount-aggfunction.md)の中間結果を計算します。 

* [集計](summarizeoperator.md)内の集計のコンテキストでのみ使用できます。

[基礎となるアルゴリズム *(H*yper*L*og*L*og) と推定精度](dcount-aggfunction.md#estimation-accuracy)について読んでください。

**構文**

`summarize``hll(` *Expr* `,` [*精度*]`)`

**引数**

* *Expr*: 集計の計算に使用する式。 
* *Accuracy*を指定した場合は、速度と精度のバランスが制御されます。
    * `0` = 精度は最も低くなりますが、計算速度は最高になります。 1.6% エラー
    * `1`= 精度と計算時間のバランスをとるデフォルト値。約0.8%の誤差。
    * `2`= 正確で遅い計算;約0.4%の誤差。
    * `3`= 余分な正確で遅い計算;約0.28%の誤差。
    * `4`= 超正確で最も遅い計算;約0.2%の誤差。
    
**戻り値**

グループ全体の*Expr*の個別のカウントの中間結果。
 
**ヒント**

1) 複数の hll 中間結果をマージするには[、hll_merge関数の](hll-merge-aggfunction.md)集計関数を使用できます (hll 出力でのみ動作します)。

2) hll / hll_merge集計関数から dcount を計算する関数[dcount_hll](dcount-hllfunction.md)を使用できます。

**使用例**

```kusto
StormEvents
| summarize hll(DamageProperty) by bin(StartTime,10m)

```

|StartTime|hll_DamageProperty|
|---|---|
|2007-09-18 20:00:00.0000000|[[1024,14],[-5473486921211236216,-6230876016761372746,3953448761157777955,4246796580750024372],[]]|
|2007-09-20 21:50:00.0000000|[[1024,14],[4835649640695509390],[]]|
|2007-09-29 08:10:00.0000000|[[1024,14],[4246796580750024372],[]]|
|2007-12-30 16:00:00.0000000|[[1024,14],[4246796580750024372,-8936707700542868125],[]]|