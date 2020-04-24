---
title: サンプル オペレーター - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのサンプル オペレーターについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: 757830bde0c56ac727d5240c01ca4768eab28877
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510032"
---
# <a name="sample-operator"></a>sample 演算子

入力テーブルから指定した数のランダム行を返します。

```kusto
T | sample 5
```

**構文**

_T_ `| sample` _番号の行_

**引数**

- _行数_: 返す_T_の行数。 任意の数値式を指定できます。

**メモ**

- `sample`は、値の分布ではなく、速度に適しています。 具体的には、異なるサイズの2つのデータセット(aまたは`union``join`演算子など)をユニオンする演算子の後に使用しても、それは「公正な」結果を生成しないことを意味します。 テーブル参照とフィルタの直後`sample`に使用することをお勧めします。

- `sample`は非決定的な演算子であり、クエリ中に評価されるたびに異なる結果セットを返します。 たとえば、次のクエリは 2 つの異なる行を生成します (同じ行を 2 回返す予定がある場合でも)。

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = _data | sample 1;
union (_sample), (_sample)
```

| x   |
| --- |
| 83  |
| 3   |

上記`_sample`の例では、1回計算されていることを確認するために[、1つは、具体化()](./materializefunction.md)関数を使用することができます。

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

- (指定した行数ではなく) データの特定の割合をサンプリングする場合は、

```kusto
StormEvents | where rand() < 0.1
```

- 行ではなくキーをサンプリングする場合 (例えば、 10 ID をサンプルし、これらの ID のすべての行を取得[`sample-distinct`](./sampledistinctoperator.md)する)、`in`演算子と組み合わせて使用できます。

**使用例**

```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents
| where EpisodeId in (sampleEpisodes)
```