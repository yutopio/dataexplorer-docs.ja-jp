---
title: set_difference() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで set_difference() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: d4edb8ec46fca99b7dd58b11bbd54442a9340c7a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507805"
---
# <a name="set_difference"></a>set_difference()

最初の`dynamic`配列にあるが、他の配列に存在しないすべての個別の値のセットの (JSON) 配列を返します ( (((arr1 \ arr2) \ arr3) \ ..)。

**構文**

`set_difference(`*arr1*`, `*arr2*`[`,` *arr3*, ...])`

**引数**

* *arr1..arrN*: 差分セットを作成するための配列を入力します (少なくとも 2 つの配列)。 すべての引数は動的配列でなければなりません[(pack_array](packarrayfunction.md)参照)。 

**戻り値**

arr1 内にあるが、他の配列に含まれているすべての個別値のセットの動的配列を返します。 と[`set_union()`](setunionfunction.md)[`set_intersect()`](setintersectfunction.md)を参照してください。

**例**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(x,y,w)
| project set_difference(a1, a2, a3)
```

|Column1|
|---|
|[4]|
|[8]|
|[12]|

```kusto
print arr = set_difference(dynamic([1,2,3]), dynamic([1,2,3]))
```

|Arr|
|---|
|[]|