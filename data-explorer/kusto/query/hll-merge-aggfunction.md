---
title: hll_merge() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの hll_merge() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 4700d5c87bf0f29f7bba86d56114a6a61092da94
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514112"
---
# <a name="hll_merge-aggregation-function"></a>hll_merge() (集計関数)

グループ全体の HLL 結果を単一の HLL 値にマージします。

* [集計](summarizeoperator.md)内の集計のコンテキストでのみ使用できます。

[基礎となるアルゴリズム *(H*yper*L*og*L*og) と推定精度](dcount-aggfunction.md#estimation-accuracy)について読んでください。

**構文**

`summarize``hll_merge(`*エクスプル*`)`

**引数**

* *Expr*: 集計の計算に使用する式。 

**戻り値**

グループ全体で*の Expr*のマージされた hll 値。
 
**ヒント**

1) hll / hll マージ集計関数から dcount を計算する関数 [dcount_hll] (dcount-hllfunction.md) を使用できます。