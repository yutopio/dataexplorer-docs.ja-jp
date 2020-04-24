---
title: row_window_session() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで row_window_session() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 87e89443bc85125e705bc180ea0cdb9e599c9c13
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510253"
---
# <a name="row_window_session"></a>row_window_session()

`row_window_session()`[シリアル化された行セット](./windowsfunctions.md#serialized-row-set)の列のセッション開始値を計算します。

**構文**

`row_window_session``(`*最初*`,``,`*Restart*の`,`*最大距離間の間の最初の距離から*Expr の最大距離 [ 再起動 ] *MaxDistanceFromFirst*`)`

* *Expr*は、セッション内で値がグループ化された式です。
  NULL 値を指定すると NULL 値が生成され、次の値が新しいセッションを開始します。
  *Expr*は型`datetime`のスカラー式でなければなりません。

* *新*しいセッションを開始するための 1 つの基準を設定します: *Expr*の現在の値とセッションの開始時の*Expr*の値の間の最大距離。
  型のスカラー定数です`timespan`。

* *新*しいセッションを開始するための 2 番目の基準を確立します: *Expr*の 1 つの値から次の値までの最大距離。
  型のスカラー定数です`timespan`。

* *再起動*は、オプションのスカラー式です`boolean`。 指定した場合、評価されるすべての値`true`は、セッションを直ちに再開します。

**戻り値**

この関数は、各セッションの開始時に値を返します。

**メモ**

この関数には、次の概念計算モデルがあります。

1. *値 Expr*の入力シーケンスを順番に確認します。

2. すべての値について、新しいセッションを確立するかどうかを判断します。

3. 新しいセッションが確立された場合は *、Expr*の値を出力します。 それ以外の場合は、以前の値の*Expr*を出力します。

この条件は、値が新しいセッションを表すかどうかを決定する条件は、次の論理 OR です。

* 以前のセッション値がなかった場合、または前のセッション値が null であった場合。

* *Expr*の値が、前のセッション値に*MaxDistanceFromFirst*を加えた値と等しいか、それを超えている場合。

* *Expr*の値が、以前の*Expr*と*最大距離間近*傍の値と等しいか、それを超えている場合。

**使用例**

次の例は、2 つの列、シーケンスを識別する`ID`列、および各レコードが発生した時刻を示す`Timestamp`列を持つテーブルのセッション開始値を計算する方法を示しています。 この例では、セッションは 1 時間を超えることはできません。

```kusto
datatable (ID:string, Timestamp:datetime) [
    // ...
]
| sort by ID asc, Timestamp asc
| extend SessionStarted = row_window_session(Timestamp, 1h, 5m, ID != prev(ID))
```