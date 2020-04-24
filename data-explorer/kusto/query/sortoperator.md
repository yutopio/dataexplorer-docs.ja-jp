---
title: ソート演算子 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの並べ替え演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 638783b28cddc51d64a80096d7d4d6e0f669d354
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507482"
---
# <a name="sort-operator"></a>sort 演算子 

入力テーブルの行を 1 つ以上の列の順序で並べ替えます。

```kusto
T | sort by strlen(country) asc, price desc
```

**エイリアス**

`order`

**構文**

*T*`| sort by`*expression*式`asc` | `desc`[`nulls first` | `nulls last`]`,` [ ] [ .]

**引数**

* *T*: ソートするテーブル入力。
* *式*: 並べ替えに使用するスカラー式。 値の型は、数値、日付、時刻、または文字列にする必要があります。
* `asc` : 昇順で (小さい値から大きい値へ) 並べ替えます。 既定値は `desc`で、降順 (大きい値から小さい値へ) です。
* `nulls first`(順序の`asc`デフォルト)は、最初に NULL 値を配置`nulls last`し、(順序`desc`のデフォルト)末尾に NULL 値を配置します。

**例**

```kusto
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc nulls first
```

特定の `ActivityId`を持つ Traces テーブルのすべての行が、タイムスタンプの順で並べ替えられます。 列`Timestamp`に NULL 値が含まれている場合、結果の最初の行に表示されます。

結果から NULL 値を除外するには、並べ替えの呼び出しの前にフィルターを追加します。

```kusto
Traces
| where ActivityId == "479671d99b7b" and isnotnull(Timestamp)
| sort by Timestamp asc
```