---
title: set_intersect ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの set_intersect () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: f611f660a791117159ad5fce4c024914d9e6909b
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264744"
---
# <a name="set_intersect"></a>set_intersect()

すべての `dynamic` 配列 (arr1 ∩ arr2 ∩...) にあるすべての個別の値のセットの配列を返します。

**構文**

`set_intersect(`*arr1* `, `*arr2* `[` 、` *arr3*, ...])`

**引数**

* *arr1...arrN*: 交差セットを作成するための入力配列 (少なくとも2つの配列)。 すべての引数は動的配列である必要があります ( [pack_array](packarrayfunction.md)を参照してください)。 

**戻り値**

すべての配列に含まれるすべての個別の値のセットの動的配列を返します。 「」および「」を参照してください [`set_union()`](setunionfunction.md) [`set_difference()`](setdifferencefunction.md) 。

**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(w,x)
| project set_intersect(a1, a2, a3)
```

|Column1|
|---|
| [1]|
|3|
|番|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr = set_intersect(dynamic([1, 2, 3]), dynamic([4,5]))
```

|→|
|---|
|[]|
