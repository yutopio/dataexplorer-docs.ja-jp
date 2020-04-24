---
title: isnan() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの isnan() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 123d9cd32d645bb1225983138973a17b6bb9ecf3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513568"
---
# <a name="isnan"></a>isnan()

入力が数値ではない (NaN) 値かどうかを返します。  

**構文**

`isnan(`*X*`)`

**引数**

* *x*: 実数。

**戻り値**

x が NaN の場合はゼロ以外の値 (true) 。それ以外の場合はゼロ (false) です。

**参照**

* 値が null かどうかを調べるには[、isnull()](isnullfunction.md)を参照してください。
* 値が有限かどうかの検査については[、isfinite()](isfinitefunction.md)を参照してください。
* 値が無限であるかどうかを確認するには[、isinf()](isinffunction.md)を参照してください。

**例**

```kusto
range x from -1 to 1 step 1
| extend y = (-1*x) 
| extend div = 1.0*x/y
| extend isnan=isnan(div)
```

|x|y|div|Isnan|
|---|---|---|---|
|-1|1|-1|0|
|0|0|(NaN)|1|
|1|-1|-1|0|