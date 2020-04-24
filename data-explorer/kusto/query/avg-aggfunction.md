---
title: avg() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの avg() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: aadc756bdf4c6cab805f58a8a600815cf29680f7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518345"
---
# <a name="avg-aggregation-function"></a>avg() (集計関数)

グループ全体の*Expr*の平均を計算します。 

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`avg(`*エクスプの*要約`)`

**引数**

* *Expr*: 集計の計算に使用する式。 値を`null`持つレコードは無視され、計算には含まれません。

**戻り値**

グループ全体の*Expr*の平均値。
 