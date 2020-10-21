---
title: row_window_session ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの row_window_session () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f872004a8291adc95f594c6301075faa02c8ec6c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242866"
---
# <a name="row_window_session"></a>row_window_session()

`row_window_session()` シリアル化された [行セット](./windowsfunctions.md#serialized-row-set)内の列のセッション開始値を計算します。

## <a name="syntax"></a>構文

`row_window_session` `(` *`Expr`* `,` *`MaxDistanceFromFirst`* `,` *`MaxDistanceBetweenNeighbors`* [`,` *`Restart`*] `)`

* *`Expr`* は、セッション内で値がグループ化された式です。
  Null 値によって null 値が生成され、次の値によって新しいセッションが開始されます。
  *`Expr`* 型のスカラー式である必要があり `datetime` ます。

* *`MaxDistanceFromFirst`* 新しいセッションを開始するための1つの条件を設定します。現在の値から、 *`Expr`* *`Expr`* セッションの開始時のの値までの最大距離です。
  これは型のスカラー定数です `timespan` 。

* *`MaxDistanceBetweenNeighbors`* 新しいセッションを開始するための2番目の条件を確立します。1つの値から次の値への最大距離です。 *`Expr`*
  これは型のスカラー定数です `timespan` 。

* *Restart* は、型の省略可能なスカラー式です `boolean` 。 指定した場合、に評価されるすべての値 `true` が直ちにセッションを再起動します。

## <a name="returns"></a>戻り値

関数は、各セッションの開始時に値を返します。

**ノート**

関数には、次の概念計算モデルがあります。

1. 入力した値のシーケンスを *`Expr`* 順番に表示します。

1. すべての値について、新しいセッションを確立するかどうかを判断します。

1. 新しいセッションを確立する場合は、の値を出力し *`Expr`* ます。 それ以外の場合は、の前の値を出力し *`Expr`* ます。

値が新しいセッションを表すかどうかを決定する条件は、論理または次のいずれかの条件になります。

* 以前のセッション値がなかった場合、または以前のセッション値が null の場合。

* の値が *`Expr`* 以前のセッションの値にプラスを加えた値に等しいか、それを超えた場合 *`MaxDistanceFromFirst`* 。

* の値が *`Expr`* またはの前の値を加算した値を超えている場合は *`Expr`* *`MaxDistanceBetweenNeighbors`* 。

## <a name="examples"></a>例

次の例では、2つの列を持つテーブルのセッション開始値を計算する方法を示します。1つはシーケンスを識別する列で、もう1つは `ID` `Timestamp` 各レコードが発生した時刻を示す列です。 この例では、セッションが1時間を超えることはできず、レコードが5分未満である間は継続されます。

```kusto
datatable (ID:string, Timestamp:datetime) [
    // ...
]
| sort by ID asc, Timestamp asc
| extend SessionStarted = row_window_session(Timestamp, 1h, 5m, ID != prev(ID))
```