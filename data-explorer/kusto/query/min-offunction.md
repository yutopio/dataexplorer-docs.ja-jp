---
title: min_of ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの min_of () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0762b1416df32279b9801c47f129a6966772a7e2
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271368"
---
# <a name="min_of"></a>min_of()

複数の評価された数値式の最小値を返します。

```kusto
min_of(10, 1, -3, 17) == -3
```

**構文**

`min_of``(` *expr_1* `,` *expr_2*を expr_1 しています...`)`

**引数**

* *expr_i*: 評価されるスカラー式。

- すべての引数は同じ型である必要があります。
- 最大64個の引数がサポートされています。

**戻り値**

すべての引数式の最小値。

**例**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=min_of(10, 1, -3, 17) 
```

|結果|
|---|
|-3|
