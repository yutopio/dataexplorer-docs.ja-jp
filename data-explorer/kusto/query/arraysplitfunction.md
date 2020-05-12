---
title: array_split ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの array_split () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/28/2018
ms.openlocfilehash: 102077c9c1116bd9476c6dae59d993a6379b69bd
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225556"
---
# <a name="array_split"></a>array_split()

分割インデックスに従って配列を複数の配列に分割し、生成された配列を動的配列にパックします。

**構文**

`array_split`(*`arr`*, *`indices`*)

**引数**

* *`arr`*: 分割する入力配列は動的配列である必要があります。
* *`indices`*: 分割インデックス (0 から始まる) を持つ整数の整数または動的配列。負の値は array_length + 値に変換されます。

**戻り値**

からの範囲内の値を持つ N + 1 配列を含む動的配列 `[0..i1), [i1..i2), ... [iN..array_length)` `arr` 。ここで、n は入力インデックスの数で、 `i1...iN` はインデックスです。

**使用例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, 2)
```

|`arr`|`arr_split`|
|---|---|
|[1、2、3、4、5]|[[1, 2]、[3, 4, 5]]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, dynamic([1,3]))
```

|`arr`|`arr_split`|
|---|---|
|[1、2、3、4、5]|[[1]、[2, 3]、[4, 5]]|
