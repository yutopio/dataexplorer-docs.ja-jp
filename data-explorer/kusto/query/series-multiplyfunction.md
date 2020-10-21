---
title: series_multiply ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_multiply () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 21095174a9b2f25a4c48fdd65c05579ed7efff7d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246082"
---
# <a name="series_multiply"></a>series_multiply()

2つの数値系列入力の要素ごとの乗算を計算します。

## <a name="syntax"></a>構文

`series_multiply(`*series1* `,`*series2*`)`

## <a name="arguments"></a>引数

* *series1, series2*: 入力数値配列。要素ごとに動的な配列の結果を乗算します。 すべての引数は動的配列である必要があります。 

## <a name="returns"></a>戻り値

2つの入力間の計算された要素ごとの乗算演算の動的配列。 数値以外の要素または存在しない要素 (異なるサイズの配列) は、 `null` 要素の値を生成します。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_multiply_s2 = series_multiply(s1, s2)
```

|s1         |s2|        s1_multiply_s2|
|---|---|---|
|[1, 2, 4]    |[4, 2, 1]|   [4, 4, 4]|
|[2, 4, 8]    |[8, 4, 2]|   [16, 16, 16]|
|[3、6、12]   |[12, 6, 3]|  [36、36、36]|
