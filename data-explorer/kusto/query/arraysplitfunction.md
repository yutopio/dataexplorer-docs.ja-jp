---
title: array_split ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの array_split () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/28/2018
ms.openlocfilehash: b1dad77bd58d64bfcd167cc7d30f0986a54f13c3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252805"
---
# <a name="array_split"></a>array_split()

分割インデックスに従って配列を複数の配列に分割し、生成された配列を動的配列にパックします。

## <a name="syntax"></a>構文

`array_split`(*`arr`*, *`indices`*)

## <a name="arguments"></a>引数

* *`arr`*: 分割する入力配列は動的配列である必要があります。
* *`indices`*: 分割インデックス (0 から始まる) を持つ整数の整数または動的配列。負の値は array_length + 値に変換されます。

## <a name="returns"></a>戻り値

からの範囲内の値を持つ N + 1 配列を含む動的配列 `[0..i1), [i1..i2), ... [iN..array_length)` `arr` 。ここで、n は入力インデックスの数で、 `i1...iN` はインデックスです。

## <a name="examples"></a>例

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
