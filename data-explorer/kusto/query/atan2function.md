---
title: atan2 ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの atan2 () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1229ff1476afe2863f07cfc0ff7aecffadd5867f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252760"
---
# <a name="atan2"></a>atan2()

正の x 軸と、原点から点 (y, x) までの射線との角度をラジアン単位で計算します。

## <a name="syntax"></a>構文

`atan2(`*y* `,`*x*`)`

## <a name="arguments"></a>引数

* *x*: x 座標 (実数)。
* *y*: y 座標 (実数)。

## <a name="returns"></a>戻り値

* 正の x 軸と、原点から地点までの射線 (y, x) の間の角度 (ラジアン)。

## <a name="examples"></a>例

```kusto
print atan2_0 = atan2(1,1) // Pi / 4 radians (45 degrees)
| extend atan2_1 = atan2(0,-1) // Pi radians (180 degrees)
| extend atan2_2 = atan2(-1,0) // - Pi / 2 radians (-90 degrees)
```

|atan2_0|atan2_1|atan2_2|
|---|---|---|
|0.785398163397448|3.14159265358979)|-1.5707963267949|