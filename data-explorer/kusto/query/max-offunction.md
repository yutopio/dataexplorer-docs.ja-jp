---
title: max_of ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの max_of () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4b912b1bdc68d7b3071ace8547f0aaf7c679a86a
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271606"
---
# <a name="max_of"></a>max_of()

複数の評価された数値式の最大値を返します。

```kusto
max_of(10, 1, -3, 17) == 17
```

**構文**

`max_of``(` *expr_1* `,` *expr_2*を expr_1 しています...`)`

**引数**

* *expr_i*: 評価されるスカラー式。

- すべての引数は同じ型である必要があります。
- 最大64個の引数がサポートされています。

**戻り値**

すべての引数式の最大値。

**例**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result = max_of(10, 1, -3, 17) 
```

|結果|
|---|
|17|
