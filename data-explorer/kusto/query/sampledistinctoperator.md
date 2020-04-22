---
title: サンプル別のオペレーター - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのサンプルに固有の演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b6d6c77aef3a7e2c6d99af792062d9f1a6215f51
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663649"
---
# <a name="sample-distinct-operator"></a>sample-distinct 演算子

要求された列について、指定された個数までの重複なしの値が含まれる 1 つの列を返します。 

オペレータのデフォルト(現在のみ)フレーバーは、できるだけ早く答えを返そうとします(公正なサンプルを作ろうとするのではなく)

```kusto
T | sample-distinct 5 of DeviceId
```

**構文**

*T* `| sample-distinct` *番号の値*`of`*列名*

**引数**
* *値の数*: 返す*T*の数値の異なる値。 任意の数値式を指定できます。

**ヒント**

 let ステートメントを入力`sample-distinct`し、演算子を使用して後でフィルター処理を行うことで`in`、母集団をサンプリングするのに便利です (例を参照)。 

 サンプルだけでなく上位の値を使用する場合は、[トップヒッター演算子を](tophittersoperator.md)使用できます。 

 データ行をサンプリングする場合 (特定の列の値ではなく) は、[サンプル演算子](sampleoperator.md)を参照してください。

**使用例**  

母集団から 10 個の異なる値を取得する

```kusto
StormEvents | sample-distinct 10 of EpisodeId

```

summarize 演算がクエリの制限を超えないことがわかっている場合に、母集団をサンプリングしてさらに計算を行います。 

```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents 
| where EpisodeId in (sampleEpisodes) 
| summarize totalInjuries=sum(InjuriesDirect) by EpisodeId
```