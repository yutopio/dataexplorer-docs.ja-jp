---
title: tdigest_merge() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでtdigest_merge() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 0b7de916dd53c19a49301c8048e2d8867d1b1249
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506394"
---
# <a name="tdigest_merge-aggregation-function"></a>tdigest_merge() (集計関数)

グループ全体で消化結果をマージします。 

* [集計](summarizeoperator.md)内の集計のコンテキストでのみ使用できます。

基礎となるアルゴリズム (T-Digest) と推定誤差の詳細[については、 こちらをご覧ください](percentiles-aggfunction.md#estimation-error-in-percentiles)。

**構文**

`tdigest_merge(` *Expr*`)`の概要を説明します。

`tdigest_merge(` *Expr* `)` - エイリアスの概要を説明します。

**引数**

* *Expr*: 集計の計算に使用する式。 

**戻り値**

グループ全体で*の Expr*のマージされたダイジェスト値。
 

**ヒント**

1) 関数 [ ]`percentile_tdigest()`(percentile-tdigestfunction.md) を使用できます。

2) 同じグループに含まれるすべての消化器は同じタイプでなければなりません。