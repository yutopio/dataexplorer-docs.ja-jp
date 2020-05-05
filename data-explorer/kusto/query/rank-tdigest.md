---
title: rank_tdigest ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの rank_tdigest () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: a849cd496d41ad473768b3f267639eaf8c467880
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741780"
---
# <a name="rank_tdigest"></a>rank_tdigest()

セット内の値のおおよその順位を計算します。 セット`v` `S`内の値のランクは`S` `v`、 `S`より小さいまたは等しいのメンバーの数として定義され`tdigest`ます。これは、によって表されます。

**構文**

`rank_tdigest(`*`TDigest`*`,` *`Expr`*`)`

**引数**

* *Tdigest*: [tdigest ()](tdigest-aggfunction.md)または[tdigest_merge ()](tdigest-merge-aggfunction.md)によって生成された式
* *Expr*: 順位付けの計算に使用される値を表す式。

**戻り値**

データセット内の rank foreach 値。

**ヒント**

1) ランクを取得する値は、 `tdigest`と同じ型である必要があります。

**使用例**

並べ替えられたリスト (1-1000) では、685のランクは次のようになります。

```kusto
range x from 1 to 1000 step 1
| summarize t_x=tdigest(x)
| project rank_of_685=rank_tdigest(t_x, 685)
```

|`rank_of_685`|
|-------------|
|`685`        |

このクエリでは、すべての損傷プロパティのコストについて、値 $4490 のランクが計算されます。

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project rank_of_4490=rank_tdigest(tdigestRes, 4490) 

```

|`rank_of_4490`|
|--------------|
|`50207`       |

(セットサイズで割ることによって) ランクの推定パーセンテージを取得します。

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty), count()
| project rank_tdigest(tdigestRes, 4490) * 100.0 / count_

```

|`Column1`         |
|------------------|
|`85.0015237192293`|


損傷プロパティのコストの百分位85は、$4490 です。

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|`percentile_tdigest_tdigestRes`|
|-------------------------------|
|`4490`                         |


