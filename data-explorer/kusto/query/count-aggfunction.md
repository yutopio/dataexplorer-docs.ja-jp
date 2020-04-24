---
title: count() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで count() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 59b898d44507d844db1f714ef15effa004e5546f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517002"
---
# <a name="count-aggregation-function"></a>カウント()(集計関数)

集計グループごとのレコード数を返します (集計がグループ化なしで行われた場合は合計で返します)。

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)
* [countif](countif-aggfunction.md)集計関数を使用して、一部の述語が`true`返すレコードのみをカウントします。

**構文**

要約`count()`

**戻り値**

集計グループごとのレコード数を返します (集計がグループ化なしで行われた場合は合計で返します)。