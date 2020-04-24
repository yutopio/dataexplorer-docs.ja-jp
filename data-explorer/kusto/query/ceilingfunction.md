---
title: 天井() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの ceiling() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f2ecd043c43bb1af6530364d200d5dc9db640f95
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517257"
---
# <a name="ceiling"></a>ceiling()

指定した数値式より大きい、または等しい最小整数を計算します。

**構文**

`ceiling(`*X*`)`

**引数**

* *x*: 実数。

**戻り値**

* 指定した数値式より大きい、または等しい最小整数。 

**使用例**

```kusto
print c1 = ceiling(-1.1), c2 = ceiling(0), c3 = ceiling(0.9)
```

|c1|c2|c3|
|---|---|---|
|-1|0|1|