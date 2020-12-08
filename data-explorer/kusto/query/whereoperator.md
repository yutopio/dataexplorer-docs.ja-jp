---
title: Kusto クエリ言語の where 演算子 - Azure Data Explorer
description: この記事では、Azure Data Explorer の where 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 6ac800cd4b38396e0f32f44976c4594c093747bb
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512011"
---
# <a name="where-operator"></a>where 演算子

述語の条件を満たす行のサブセットにテーブルをフィルター処理します。

```kusto
T | where fruit=="apple"
```

**Alias** `filter`

## <a name="syntax"></a>構文

*T* `| where` *Predicate*

## <a name="arguments"></a>引数

* *T*:フィルター処理するレコードが含まれる表形式の入力。
* *[述語]* :*T* の列に対する `boolean` [式](./scalar-data-types/bool.md)。*T* 内の各行について評価されます。

## <a name="returns"></a>戻り値

*Predicate* が `true` である *T* 内の行。

**注** Null 値: null 値と比較された場合、すべてのフィルター関数によって false が返されます。 次の特殊な null 対応関数を使用して、null 値を処理するクエリを記述できます。

[isnull()](./isnullfunction.md)、[isnotnull()](./isnotnullfunction.md)、[isempty()](./isemptyfunction.md)、[isnotempty()](./isnotemptyfunction.md) 

**ヒント**

パフォーマンスを最大限高めるためのヒントを示します。

* **シンプルな比較を使う** ("定数" とは、テーブルに対する定数です。 ("定数" とは、テーブルに対する定数です。`now()` と `ago()` は適切で、[`let` ステートメント](./letstatement.md)を使って割り当てられるスカラー値も同様です)。

    たとえば、`where floor(Timestamp, 1d) == ago(1d)` よりも `where Timestamp >= ago(1d)` の方が適切です。

* **最もシンプルな語句を先頭に配置する**: `and` で連結された複数の句がある場合は、関係する列が 1 つしかない句を先頭に配置します。 そのため、 `Timestamp > ago(1d) and OpId == EventId` の方が、逆の順序にするよりも適切です。

詳細については、[使用可能な String 演算子](./datatypes-string-operators.md)の概要および[使用可能な数値演算子](./numoperators.md)の概要を参照してください。

## <a name="example-simple-comparisons-first"></a>例:最初の単純な比較

```kusto
Traces
| where Timestamp > ago(1h)
    and Source == "MyCluster"
    and ActivityId == SubActivityId 
```

この例では、1 時間以内のレコードを取得しています。レコードは `MyCluster` というソースに由来し、同じ値の 2 つの列があります。 

2 つの列の比較を最後に配置していることに注目してください。これは、インデックスを使用できず、スキャンを強制するためです。

## <a name="example-columns-contain-string"></a>例:文字列を含む列

```kusto
Traces | where * has "Kusto"
```

任意の列に "Kusto" という単語が表示されるすべての行。
 
