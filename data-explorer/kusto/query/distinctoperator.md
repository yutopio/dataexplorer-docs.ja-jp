---
title: 個別のオペレーター - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの個別のオペレーターについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4287ca48d3fb5006e67a9266ea05287a7d8a06f6
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030372"
---
# <a name="distinct-operator"></a>distinct 演算子

入力テーブルの指定された列の異なる組み合わせを持つテーブルを生成します。 

```kusto
T | distinct Column1, Column2
```

入力テーブル内のすべての列の個別の組み合わせを持つテーブルを生成します。

```kusto
T | distinct *
```

**例**

果物と価格の明確な組み合わせを示します。

```kusto
Table | distinct fruit, price
```

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="異なる":::

**メモ**

* 演算子`summarize by ...`は、`distinct`グループ キーとしてアスタリスク`*`( ) を指定でき、ワイド テーブルの使用が容易になります。
* キーによるグループが高いカーディナリティである場合`summarize by ...`は、[シャッフル戦略](shufflequery.md)を使用すると便利です。