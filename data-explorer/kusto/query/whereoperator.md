---
title: where 演算子-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの where 演算子 (contains、contains、startswith、endswith、regex の一致) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fadf8aa8c21dac364793c73a38e68d55fc2a6f6d
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370366"
---
# <a name="where-operator"></a>where 演算子

述語の条件を満たす行のサブセットにテーブルをフィルター処理します。

```kusto
T | where fruit=="apple"
```

**エイリアス**`filter`

**構文**

*T* `| where` *述語*

**引数**

* *T*: レコードをフィルター処理するための表形式の入力。
* *述語*: `boolean` *T*の列に対する[式](./scalar-data-types/bool.md)。*T*の各行に対して評価されます。

**戻り値**

*Predicate* が `true` である *T* 内の行。

**メモ**Null 値: すべてのフィルター処理関数は、null 値と比較した場合に false を返します。 特殊な null 対応関数を使用して、null 値を受け取るクエリを作成することができます[isnull ()](./isnullfunction.md),、 [isnotnull ()](./isnotnullfunction.md),、 [isempty (](./isemptyfunction.md)),、 [isnotempty ()](./isnotemptyfunction.md)です。 

**ヒント**

パフォーマンスを最大限高めるためのヒントを示します。

* **シンプルな比較を使う** ("定数" とは、テーブルに対する定数です。 (' Constant ' はテーブルに対して定数を意味します。そのため、 `now()` と `ago()` は OK です。したがって、 [ `let` ステートメント](./letstatement.md)を使用してスカラー値が割り当てられます)。

    たとえば、`where floor(Timestamp, 1d) == ago(1d)` よりも `where Timestamp >= ago(1d)` の方が適切です。

* **最もシンプルな語句を先頭に配置する**: `and` で連結された複数の句がある場合は、関係する列が 1 つしかない句を先頭に配置します。 そのため、 `Timestamp > ago(1d) and OpId == EventId` の方が、逆の順序にするよりも適切です。

詳細については、[使用可能な文字列演算子](./datatypes-string-operators.md)の概要と、[使用可能な数値演算子](./numoperators.md)の概要を参照してください。

**例**

```kusto
Traces
| where Timestamp > ago(1h)
    and Source == "MyCluster"
    and ActivityId == SubActivityId 
```

1時間経過しておらず、"MyCluster" という名前のソースから取得され、同じ値を持つ2つの列を持つレコード。 

2 つの列の比較を最後に配置していることに注目してください。これは、インデックスを使用できず、スキャンを強制するためです。

**例**

```kusto
Traces | where * has "Kusto"
```

"Kusto" という単語が任意の列に表示されているすべての行。
