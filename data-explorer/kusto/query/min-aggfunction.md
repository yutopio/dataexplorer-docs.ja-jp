---
title: min() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの min() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/24/2019
ms.openlocfilehash: ca50210c84b39f19e6717b27089313d0d116e21a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512412"
---
# <a name="min-aggregation-function"></a>min() (集計関数)

グループ全体の最小値を返します。 

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`summarize``min(`*エクスプル*`)`

**引数**

* *Expr*: 集計の計算に使用する式。 

**戻り値**

グループ全体で*の Expr*の最小値。
 
> [!TIP]
> これは、独自に最小または最大を与える - 例えば、最高または最低価格。 しかし、行に他の列 (例えば、最低価格のサプライヤーの名前) が必要な場合は[、arg_max](arg-max-aggfunction.md)または[arg_min](arg-min-aggfunction.md)を使用します。