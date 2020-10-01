---
title: countif () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの countif () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/02/2020
ms.openlocfilehash: 9e81b4947f3a3a0b1102256cb7fd2f635ce4611b
ms.sourcegitcommit: 1618cbad18f92cf0cda85cb79a5cc1aa789a2db7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2020
ms.locfileid: "91614979"
---
# <a name="countif-aggregation-function"></a>countif () (集計関数)

*Predicate* が `true` と評価された行の数を返します。 集計のコンテキストでのみ使用でき[ます。](summarizeoperator.md)

## <a name="syntax"></a>構文

`countif(`*述語*の集計`)`

## <a name="arguments"></a>引数

*述語*: 集計計算に使用される式。 *述語* は、戻り値の型が bool (true/false に評価されます) の任意のスカラー式にすることができます。

## <a name="returns"></a>戻り値

*Predicate* が `true` と評価された行の数を返します。

## <a name="example"></a>例

```kusto
let T = datatable(name:string, day_of_birth:long)
[
   "John", 9,
   "Paul", 18,
   "George", 25,
   "Ringo", 7
];
T
| summarize countif(strlen(name) > 4)
```

|countif_|
|----|
|2|

## <a name="see-also"></a>関連項目

[count ()](count-aggfunction.md) 関数。述語式のない行をカウントします。
