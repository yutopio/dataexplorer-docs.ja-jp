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
ms.openlocfilehash: 143257a586bb951caeb116882551e55f89c8636e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345880"
---
# <a name="rank_tdigest"></a>rank_tdigest()

セット内の値のおおよその順位を計算します。 セット内の値のランク `v` `S` は、より小さいまたは等しいのメンバーの数として定義され `S` ます。これは、に `v` `S` よって表され `tdigest` ます。

## <a name="syntax"></a>構文

`rank_tdigest(`*`TDigest`*`,` *`Expr`*`)`

## <a name="arguments"></a>引数

* *Tdigest*: [tdigest ()](tdigest-aggfunction.md)または[tdigest_merge ()](tdigest-merge-aggfunction.md)によって生成された式
* *Expr*: 順位付けの計算に使用される値を表す式。

## <a name="returns"></a>戻り値

データセット内の rank foreach 値。

**ヒント**

1) ランクを取得する値は、と同じ型である必要があり `tdigest` ます。

## <a name="examples"></a>例

並べ替えられたリスト (1-1000) では、685のランクは次のようになります。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 1000 step 1
| summarize t_x=tdigest(x)
| project rank_of_685=rank_tdigest(t_x, 685)
```

|`rank_of_685`|
|-------------|
|`685`        |

このクエリでは、すべての損傷プロパティのコストについて、値 $4490 のランクが計算されます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project rank_of_4490=rank_tdigest(tdigestRes, 4490) 

```

|`rank_of_4490`|
|--------------|
|`50207`       |

(セットサイズで割ることによって) ランクの推定パーセンテージを取得します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty), count()
| project rank_tdigest(tdigestRes, 4490) * 100.0 / count_

```

|`Column1`         |
|------------------|
|`85.0015237192293`|


損傷プロパティのコストの百分位85は、$4490 です。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|`percentile_tdigest_tdigestRes`|
|-------------------------------|
|`4490`                         |


