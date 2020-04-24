---
title: countif() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで countif() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 03b6f1cb959706463a73d8aa18a11144e2123492
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516985"
---
# <a name="countif-aggregation-function"></a>カウント・ファク(集計関数)

*Predicate* が `true` と評価された行の数を返します。

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

述語式のない行をカウントする[count()](count-aggfunction.md)関数も参照してください。

**構文**

`countif(`*述語*の要約`)`

**引数**

* *述語*: 集計計算に使用される式。 

**戻り値**

*Predicate* が `true` と評価された行の数を返します。

> [!TIP]
> `where filter | summarize count()` の代わりの `summarize countif(filter)` の使用