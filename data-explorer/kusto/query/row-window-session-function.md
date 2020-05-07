---
title: row_window_session ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの row_window_session () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 51bc5e26cdc2d002b29ec435a68421ba8768a7a4
ms.sourcegitcommit: 9fe6ee7db15a5cc92150d3eac0ee175f538953d2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2020
ms.locfileid: "82907191"
---
# <a name="row_window_session"></a>row_window_session()

`row_window_session()`シリアル化された[行セット](./windowsfunctions.md#serialized-row-set)内の列のセッション開始値を計算します。

**構文**

`row_window_session` `(` *`Expr`* `,` *`MaxDistanceFromFirst`* `,` *`MaxDistanceBetweenNeighbors`* [`,` *`Restart`*] `)`

* *`Expr`* は、セッション内で値がグループ化された式です。
  Null 値によって null 値が生成され、次の値によって新しいセッションが開始されます。
  *`Expr`* 型`datetime`のスカラー式である必要があります。

* *`MaxDistanceFromFirst`* 新しいセッションを開始するための1つの条件を設定します。現在*`Expr`* の値から、 *`Expr`* セッションの開始時のの値までの最大距離です。
  これは型`timespan`のスカラー定数です。

* *`MaxDistanceBetweenNeighbors`* 新しいセッションを開始するための2番目の条件を確立します。 *`Expr`* 1 つの値から次の値への最大距離です。
  これは型`timespan`のスカラー定数です。

* *Restart*は、型`boolean`の省略可能なスカラー式です。 指定した場合、に`true`評価されるすべての値が直ちにセッションを再起動します。

**戻り値**

関数は、各セッションの開始時に値を返します。

**メモ**

関数には、次の概念計算モデルがあります。

1. 入力した値*`Expr`* のシーケンスを順番に表示します。

1. すべての値について、新しいセッションを確立するかどうかを判断します。

1. 新しいセッションを確立する場合は、の*`Expr`* 値を出力します。 それ以外の場合は、の*`Expr`* 前の値を出力します。

値が新しいセッションを表すかどうかを決定する条件は、論理または次のいずれかの条件になります。

* 以前のセッション値がなかった場合、または以前のセッション値が null の場合。

* の*`Expr`* 値が以前のセッションの値にプラスを加え*`MaxDistanceFromFirst`* た値に等しいか、それを超えた場合。

* の*`Expr`* 値がまたはの前の値を*`Expr`* 加算*`MaxDistanceBetweenNeighbors`* した値を超えている場合は。

**使用例**

次の例では、2つの列を持つテーブルのセッション開始値を計算`ID`する方法を示します。1つ`Timestamp`はシーケンスを識別する列で、もう1つは各レコードが発生した時刻を示す列です。 この例では、セッションが1時間を超えることはできず、レコードが5分未満である間は継続されます。

```kusto
datatable (ID:string, Timestamp:datetime) [
    // ...
]
| sort by ID asc, Timestamp asc
| extend SessionStarted = row_window_session(Timestamp, 1h, 5m, ID != prev(ID))
```