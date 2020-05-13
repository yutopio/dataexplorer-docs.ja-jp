---
title: サンプル演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのサンプル演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: 4915371127acd229845cc9eac1ea1400484c313f
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372980"
---
# <a name="sample-operator"></a>sample 演算子

入力テーブルから、指定した数のランダムな行を返します。

```kusto
T | sample 5
```

**構文**

_T_ `| sample` _numberofrows_

**引数**

- _Numberofrows_: 返す_T_の行数。 任意の数値式を指定できます。

**メモ**

- `sample`は、値の均等な分布ではなく、速度を重視しています。 具体的には、異なるサイズの2つのデータセット (or 演算子など) を共用する演算子の後で使用した場合、"公正な" 結果が生成されないことを意味し `union` `join` ます。 テーブル参照とフィルターの直後にを使用することをお勧めし `sample` ます。

- `sample`は非決定的な演算子であり、クエリ中に評価されるたびに異なる結果セットを返します。 たとえば、次のクエリでは、2つの異なる行が生成されます (同じ行が2回返されることが予想される場合でも)。

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = _data | sample 1;
union (_sample), (_sample)
```

| x   |
| --- |
| 83  |
| 3   |

上記の例のが1回だけ計算されるようにするために `_sample` 、[具体化 ()](./materializefunction.md)関数を使用できます。

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = materialize(_data | sample 1);
union (_sample), (_sample)
```

| x   |
| --- |
| 34  |
| 34  |

**ヒント**

- 指定された数の行ではなく、データの特定の割合をサンプリングするには、を使用します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | where rand() < 0.1
```

- 行ではなく、キーをサンプリングする場合 (例: サンプル10の Id と、これらの Id のすべての行を取得する場合) は [`sample-distinct`](./sampledistinctoperator.md) 、演算子と組み合わせて使用でき `in` ます。

**使用例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents
| where EpisodeId in (sampleEpisodes)
```
