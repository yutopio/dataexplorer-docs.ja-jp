---
title: どこで演算子 (含む、含む、開始して、終わる、正規表現と一致する) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで演算子 (含む、含む、開始、終わり、正規表現と一致する場所) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a0e1486691740600fb5de4316982e8d43b13ce3a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504303"
---
# <a name="where-operator-has-contains-startswith-endswith-matches-regex"></a>演算子 (含む、含む、開始する、終わる、正規表現と一致する)

述語の条件を満たす行のサブセットにテーブルをフィルター処理します。

```kusto
T | where fruit=="apple"
```

**エイリアス** `filter`

**構文**

*T* `| where` *述語*

**引数**

* *T*: レコードをフィルター処理する表形式の入力。
* *述語* `boolean` : *T*の列に対する[式](./scalar-data-types/bool.md)。T の各*行に対*して評価されます。

**戻り値**

*Predicate* が `true` である *T* 内の行。

**注**Null 値: すべてのフィルター処理関数は、null 値と比較すると false を返します。 特殊な null 対応関数を使用して、null 値を考慮に入れてクエリを作成できます[isnotnull()](./isnotnullfunction.md)[。](./isnullfunction.md) [isempty()](./isemptyfunction.md) [isnotempty()](./isnotemptyfunction.md) 

**ヒント**

パフォーマンスを最大限高めるためのヒントを示します。

* **シンプルな比較を使う** ("定数" とは、テーブルに対する定数です。 ('定数' はテーブル上で定数`now()`を意味`ago()`するので、OK なので、[`let`ステートメント](./letstatement.md)を使用して割り当てられたスカラー値もそうです。

    たとえば、`where floor(Timestamp, 1d) == ago(1d)` よりも `where Timestamp >= ago(1d)` の方が適切です。

* **最もシンプルな語句を先頭に配置する**: `and` で連結された複数の句がある場合は、関係する列が 1 つしかない句を先頭に配置します。 そのため、 `Timestamp > ago(1d) and OpId == EventId` の方が、逆の順序にするよりも適切です。

詳細については、[使用可能な String 演算子](./datatypes-string-operators.md)の概要と[使用可能な数値演算子](./numoperators.md)の概要を参照してください。

**例**

```kusto
Traces
| where Timestamp > ago(1h)
    and Source == "MyCluster"
    and ActivityId == SubActivityId 
```

1 時間より前のレコードで、"MyCluster" というソースから取得され、同じ値の 2 つの列を持つレコード。 

2 つの列の比較を最後に配置していることに注目してください。これは、インデックスを使用できず、スキャンを強制するためです。

**例**

```kusto
Traces | where * has "Kusto"
```

任意の列に "Kusto" という単語が表示されているすべての行。