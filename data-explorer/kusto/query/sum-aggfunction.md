---
title: sum() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで sum() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: b729d9be8aba9683a053ef80acb3936c0c64d6a8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506683"
---
# <a name="sum-aggregation-function"></a>sum() (集計関数)

グループ全体の*Expr*の合計を計算します。 

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`sum(`*エクスプの*要約`)`

**引数**

* *Expr*: 集計の計算に使用する式。 

**戻り値**

グループ全体の*Expr*の合計値。
 