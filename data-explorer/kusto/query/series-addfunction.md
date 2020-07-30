---
title: series_add ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_add () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 7b6de7d141f02703c5f369dd831d1fbac82cb45e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345523"
---
# <a name="series_add"></a>series_add()

2つの数値系列入力の要素ごとの加算を計算します。

## <a name="syntax"></a>構文

`series_add(`*series1* `,`*series2*`)`

## <a name="arguments"></a>引数

* *series1, series2*: 動的配列の結果に要素ごとに追加される数値配列を入力します。 すべての引数は動的配列である必要があります。 

## <a name="returns"></a>戻り値

2つの入力間の計算された要素ごとの加算演算の動的配列。 数値以外の要素または存在しない要素 (異なるサイズの配列) は、 `null` 要素の値を生成します。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_add_s2 = series_add(s1, s2)
```

|s1|s2|s1_add_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[5、4、5]|
|[2, 4, 8]|[8, 4, 2]|[10, 8, 10]|
|[3、6、12]|[12, 6, 3]|[15, 12, 15]|
