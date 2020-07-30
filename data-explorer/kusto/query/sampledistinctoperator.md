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
ms.openlocfilehash: 3cb1de08604964d4d71c5868ef7564c728b1f2c4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351541"
---
# <a name="sample-distinct-operator"></a>sample-distinct 演算子

要求された列について、指定された個数までの重複なしの値が含まれる 1 つの列を返します。 

演算子の既定 (および現在のみ) のフレーバーは、(公平なサンプルを作成するのではなく) できるだけ早く回答を返すように試みます。

```kusto
T | sample-distinct 5 of DeviceId
```

## <a name="syntax"></a>構文

*T* `| sample-distinct` *numberofvalues* `of` *ColumnName*

## <a name="arguments"></a>引数
* *Numberofvalues*: 返される*T*の個別の値の数。 任意の数値式を指定できます。

**ヒント**

 Let ステートメントを使用して後でフィルター処理を行うことで、母集団をサンプリングすることができます `sample-distinct` `in` (例を参照)。 

 単なるサンプルではなく、最上位の値が必要な場合は、 [top-hitters](tophittersoperator.md)演算子を使用できます。 

 (特定の列の値ではなく) データ行をサンプリングする場合は、「 [sample 演算子](sampleoperator.md)」を参照してください。

## <a name="examples"></a>例  

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
