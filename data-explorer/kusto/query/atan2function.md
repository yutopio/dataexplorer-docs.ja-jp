---
title: atan2() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで atan2() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c8b49191c9d955cf5a91bde2032798f4703f7910
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518396"
---
# <a name="atan2"></a>atan2()

原点から点(y,x)までの正の X 軸と光線の間の角度をラジアン単位で計算します。

**構文**

`atan2(`*y*`,`*x*`)`

**引数**

* *x*: X 座標 (実数)。
* *y*: Y 座標 (実数)。

**戻り値**

* 原点から点(y,x)までの正の X 軸と光線の間の角度をラジアン単位で指定します。

**使用例**

```kusto
print atan2_0 = atan2(1,1) // Pi / 4 radians (45 degrees)
| extend atan2_1 = atan2(0,-1) // Pi radians (180 degrees)
| extend atan2_2 = atan2(-1,0) // - Pi / 2 radians (-90 degrees)
```

|atan2_0|atan2_1|atan2_2|
|---|---|---|
|0.785398163397448|3.14159265358979|-1.5707963267949|