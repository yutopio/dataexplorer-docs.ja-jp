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
ms.openlocfilehash: 669978d8828f54926a8535f199ef7a9bc2ba7451
ms.sourcegitcommit: d9fbcd6c9787f90de62e8e832c92d43b8090cbfc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87515772"
---
# <a name="countif-aggregation-function"></a>countif () (集計関数)

*Predicate* が `true` と評価された行の数を返します。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

「- [Count ()](count-aggfunction.md)関数」を参照してください。この関数は、述語式のない行をカウントします。

## <a name="syntax"></a>構文

`countif(`*述語*の集計`)`

## <a name="arguments"></a>引数

*述語*: 集計計算に使用される式。 *述語*は、戻り値の型が bool (true/false に評価されます) の任意のスカラー式にすることができます。

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

