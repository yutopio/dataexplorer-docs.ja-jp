---
title: sort 操作-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの並べ替え演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4f9878b77ad2288395a54d5315864e460ca37875
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351031"
---
# <a name="sort-operator"></a>sort 演算子 

入力テーブルの行を 1 つ以上の列の順序で並べ替えます。

```kusto
T | sort by strlen(country) asc, price desc
```

**Alias**

`order`

## <a name="syntax"></a>構文

*T* `| sort by` *式*[ `asc`  |  `desc` ] [ `nulls first`  |  `nulls last` ] [ `,` ...]

## <a name="arguments"></a>引数

* *T*: 並べ替えの対象となるテーブル入力。
* *式*: の並べ替えに使用するスカラー式。 値の型は、数値、日付、時刻、または文字列にする必要があります。
* `asc` : 昇順で (小さい値から大きい値へ) 並べ替えます。 既定値は `desc`で、降順 (大きい値から小さい値へ) です。
* `nulls first`(order の既定値 `asc` ) は、先頭に null 値を挿入し `nulls last` ます。(order の既定値 `desc` ) は、null 値を末尾に配置します。

## <a name="example"></a>例

```kusto
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc nulls first
```

特定の `ActivityId`を持つ Traces テーブルのすべての行が、タイムスタンプの順で並べ替えられます。 `Timestamp`列に null 値が含まれている場合は、結果の最初の行に表示されます。

結果から null 値を除外するには、並べ替えの呼び出しの前にフィルターを追加します。

```kusto
Traces
| where ActivityId == "479671d99b7b" and isnotnull(Timestamp)
| sort by Timestamp asc
```