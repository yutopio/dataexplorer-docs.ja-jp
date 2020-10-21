---
title: isnan ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの isnan () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a8cde58db626ca7bc3433e8e36c8d369a7e592b1
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253240"
---
# <a name="isnan"></a>isnan()

入力が非数 (NaN) 値かどうかを返します。  

## <a name="syntax"></a>構文

`isnan(`*閉じる*`)`

## <a name="arguments"></a>引数

* *x*: 実数。

## <a name="returns"></a>戻り値

X が NaN の場合は0以外の値 (true)。それ以外の場合は 0 (false) です。

## <a name="see-also"></a>関連項目

* 値が null かどうかを確認する方法については、「 [isnull ()](isnullfunction.md)」を参照してください。
* 値が有限かどうかを確認する方法については、「 [isfinite ()](isfinitefunction.md)」を参照してください。
* 値が無制限かどうかを確認する方法については、「 [isinf ()](isinffunction.md)」を参照してください。

## <a name="example"></a>例

```kusto
range x from -1 to 1 step 1
| extend y = (-1*x) 
| extend div = 1.0*x/y
| extend isnan=isnan(div)
```

|x|y|div|isnan|
|---|---|---|---|
|-1|1|-1|0|
|0|0|NaN|1|
|1|-1|-1|0|