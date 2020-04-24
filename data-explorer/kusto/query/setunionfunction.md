---
title: set_union() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでset_union() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: de9220ea6f9e23e458dd379fb164317842a48c94
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507669"
---
# <a name="set_union"></a>set_union()

いずれかの配列`dynamic`にあるすべての個別値のセットの (JSON) 配列を返します( arr1 00arr22 ~..

**構文**

`set_union(`*arr1*`, `*arr2*`[`,` *arr3*, ...]``)`

**引数**

* *arr1..arrN*: 共用体セットを作成するための配列を入力します (少なくとも 2 つの配列)。 すべての引数は動的配列でなければなりません[(pack_array](packarrayfunction.md)参照)。 

**戻り値**

配列の中にある、すべての個別の値のセットの動的配列を返します。 と[`set_intersect()`](setintersectfunction.md)[`set_difference()`](setdifferencefunction.md)を参照してください。

**例**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(w)
| project set_union(a1, a2, a3)
```

|Column1|
|---|
|[1,2,4,8]|
|[2,4,8,16]|
|[3,6,12,24]|