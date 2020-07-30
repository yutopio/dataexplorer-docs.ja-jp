---
title: isnan ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの isnan () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5597f21d5e426329e2793978a6b207efc3868d13
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347223"
---
# <a name="isnan"></a>isnan()

入力が非数 (NaN) 値かどうかを返します。  

## <a name="syntax"></a>構文

`isnan(`*閉じる*`)`

## <a name="arguments"></a>引数

* *x*: 実数。

## <a name="returns"></a>戻り値

X が NaN の場合は0以外の値 (true)。それ以外の場合は 0 (false) です。

**参照**

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

|x|Y|div|isnan|
|---|---|---|---|
|-1|1|-1|0|
|0|0|NaN|1|
|1|-1|-1|0|