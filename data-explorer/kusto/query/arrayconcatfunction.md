---
title: array_concat ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの array_concat () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4c8e2da4d2ba4ed205987b5a1d063ac2e75ed289
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246910"
---
# <a name="array_concat"></a>array_concat()

複数の動的配列を連結して1つの配列にします。

## <a name="syntax"></a>構文

`array_concat(`*arr1* `[` 、 ` *arr2*, ...]` ) '

## <a name="arguments"></a>引数

* *arr1...arrN*: 動的配列に連結する入力配列。 すべての引数は動的配列である必要があります ( [pack_array](packarrayfunction.md)を参照してください)。 

## <a name="returns"></a>戻り値

Arr1、arr2、...、arrN を含む配列の動的配列。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend a1 = pack_array(x,y,z), a2 = pack_array(x, y)
| project array_concat(a1, a2)
```

|列 1|
|---|
|[1, 2, 4, 1, 2]|
|[2、4、8、2、4]|
|[3、6、12、3、6]|
