---
title: rank_tdigest() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの rank_tdigest() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: ea24213b0ca673c39f399c3a12cc54cd7d7f47d5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510542"
---
# <a name="rank_tdigest"></a>rank_tdigest()

セット内の値のおおよそのランクを計算します。 `v`セット`S`内の値のランクは、のメンバー`S`の数が小さいか等しいと`v`定義され`S`、それは、それが消化不良で表されます。

**構文**

`rank_tdigest(`*Tダイジェスト*`,`*エクスプ*`)`

**引数**

* *TDigest*: [tdigest()](tdigest-aggfunction.md)または[tdigest_merge()](tdigest-merge-aggfunction.md)によって生成された式
* *Expr*: ランク付け計算に使用する値を表す式。

**戻り値**

データ セット内のランク foreach 値。

**ヒント**

1) ランクを取得する値は、ダイジェストと同じタイプである必要があります。

**使用例**

ソートされたリスト(1-1000)では、685のランクはインデックスです。

```kusto
range x from 1 to 1000 step 1
| summarize t_x=tdigest(x)
| project rank_of_685=rank_tdigest(t_x, 685)
```

|`rank_of_685`|
|-------------|
|`685`        |

このクエリは、すべての損害特性コストに対する値4490$のランクを計算します。

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project rank_of_4490=rank_tdigest(tdigestRes, 4490) 

```

|`rank_of_4490`|
|--------------|
|`50207`       |

ランクの推定パーセンテージを取得する (設定されたサイズで割る):

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty), count()
| project rank_tdigest(tdigestRes, 4490) * 100.0 / count_

```

|`Column1`         |
|------------------|
|`85.0015237192293`|


損害特性コストのパーセンタイル85は4490$ です。

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|`percentile_tdigest_tdigestRes`|
|-------------------------------|
|`4490`                         |


