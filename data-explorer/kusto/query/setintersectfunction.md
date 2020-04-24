---
title: set_intersect() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで set_intersect() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 0a1ef86573a408f644e26b3b23f0db42e327573a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507754"
---
# <a name="set_intersect"></a>set_intersect()

すべての配列`dynamic`にあるすべての個別値のセットの (JSON) 配列を返します ( arr1 000a22 ~ .. ) 。

**構文**

`set_intersect(`*arr1*`, `*arr2*`[`,` *arr3*, ...])`

**引数**

* *arr1..arrN*: 交差セットを作成するための配列を入力します (少なくとも 2 つの配列)。 すべての引数は動的配列でなければなりません[(pack_array](packarrayfunction.md)参照)。 

**戻り値**

すべての配列内にあるすべての個別値のセットの動的配列を返します。 と[`set_union()`](setunionfunction.md)[`set_difference()`](setdifferencefunction.md)を参照してください。

**例**

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
|[1]|
|[2]|
|[3]|

```kusto
print arr = set_intersect(dynamic([1, 2, 3]), dynamic([4,5]))
```

|Arr|
|---|
|[]|