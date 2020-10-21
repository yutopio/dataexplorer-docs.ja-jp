---
title: series_pearson_correlation ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの series_pearson_correlation () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/31/2019
ms.openlocfilehash: aa75e3cb2f2aefbc50c148486cb687841408e1d4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245968"
---
# <a name="series_pearson_correlation"></a>series_pearson_correlation()

2つの数値系列入力のピアソン相関係数を計算します。

参照: [ピアソン相関係数](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient)。

## <a name="syntax"></a>構文

`series_pearson_correlation(`*Series1* `,`*Series2*`)`

## <a name="arguments"></a>引数

* *Series1, Series2*: 相関係数を計算するための入力数値配列。 すべての引数は、同じ長さの動的配列である必要があります。 

## <a name="returns"></a>戻り値

2つの入力間の、計算されたピアソンの相関係数。 数値以外の要素または存在しない要素 (異なるサイズの配列) は、 `null` 結果を生成します。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range s1 from 1 to 5 step 1 | extend s2 = 2*s1 // Perfect correlation
| summarize s1 = make_list(s1), s2 = make_list(s2)
| extend correlation_coefficient = series_pearson_correlation(s1,s2)
```

|s1|s2|correlation_coefficient|
|---|---|---|
|[1、2、3、4、5]|[2、4、6、8、10]|1|
