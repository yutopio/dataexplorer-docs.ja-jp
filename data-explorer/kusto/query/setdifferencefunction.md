---
title: set_difference ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの set_difference () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 7e13a9b652e1bdadb325cd866bddd78761b25b85
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372391"
---
# <a name="set_difference"></a>set_difference()

最初の `dynamic` 配列に含まれるが、他の配列 (((arr1 \ arr2) \ arr3) \...) にはない、すべての個別の値のセットの (JSON) 配列を返します。

**構文**

`set_difference(`*arr1* `, `*arr2* `[` 、` *arr3*, ...])`

**引数**

* *arr1...arrN*: 差集合 (少なくとも2つの配列) を作成するための入力配列。 すべての引数は動的配列である必要があります ( [pack_array](packarrayfunction.md)を参照してください)。 

**戻り値**

Arr1 内にあるが、他の配列にはないすべての個別の値のセットの動的配列を返します。 「」および「」を参照してください [`set_union()`](setunionfunction.md) [`set_intersect()`](setintersectfunction.md) 。

**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|8|
|vdc|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr = set_difference(dynamic([1,2,3]), dynamic([1,2,3]))
```

|→|
|---|
|[]|