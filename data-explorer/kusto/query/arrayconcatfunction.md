---
title: array_concat() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで array_concat() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: c66c17ab147eb3d6c5f749e7f28fad347a50ce22
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518753"
---
# <a name="array_concat"></a>array_concat()

多数の動的配列を 1 つの配列に連結します。

**構文**

`array_concat(`*arr1*`[`` *arr2*, ...]`,)

**引数**

* *arr1..arrN*: 動的配列に連結される入力配列。 すべての引数は動的配列でなければなりません[(pack_array](packarrayfunction.md)参照)。 

**戻り値**

arr1、arr2、...、arrN を持つ配列の動的配列。

**例**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend a1 = pack_array(x,y,z), a2 = pack_array(x, y)
| project array_concat(a1, a2)
```

|Column1|
|---|
|[1,2,4,1,2]|
|[2,4,8,2,4]|
|[3,6,12,3,6]|