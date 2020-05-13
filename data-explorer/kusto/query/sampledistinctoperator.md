---
title: サンプル-distinct 演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのサンプル-distinct 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5303801b983b326310065ea2a6ce6ded7d098001
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373007"
---
# <a name="sample-distinct-operator"></a>sample-distinct 演算子

要求された列について、指定された個数までの重複なしの値が含まれる 1 つの列を返します。 

演算子の既定 (および現在のみ) のフレーバーは、(公平なサンプルを作成するのではなく) できるだけ早く回答を返すように試みます。

```kusto
T | sample-distinct 5 of DeviceId
```

**構文**

*T* `| sample-distinct` *numberofvalues* `of` *ColumnName*

**引数**
* *Numberofvalues*: 返される*T*の個別の値の数。 任意の数値式を指定できます。

**ヒント**

 Let ステートメントを使用して後でフィルター処理を行うことで、母集団をサンプリングすることができます `sample-distinct` `in` (例を参照)。 

 単なるサンプルではなく、最上位の値が必要な場合は、 [top-hitters](tophittersoperator.md)演算子を使用できます。 

 (特定の列の値ではなく) データ行をサンプリングする場合は、「 [sample 演算子](sampleoperator.md)」を参照してください。

**使用例**  

母集団から10個の個別の値を取得する

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | sample-distinct 10 of EpisodeId

```

summarize 演算がクエリの制限を超えないことがわかっている場合に、母集団をサンプリングしてさらに計算を行います。 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents 
| where EpisodeId in (sampleEpisodes) 
| summarize totalInjuries=sum(InjuriesDirect) by EpisodeId
```
