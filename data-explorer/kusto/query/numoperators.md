---
title: 数値演算子-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの数値演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e020d5f6692c8c290b6a68b2ed1b52bc710c89c8
ms.sourcegitcommit: 574296b9a84084de031684a65f32b6c1bd1a4858
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94713991"
---
# <a name="numerical-operators"></a>数値演算子

型、 `int` `long` 、およびは `real` 数値型を表します。
次の演算子は、これらの型のペアの間で使用できます。

演算子       |[説明]                         |例
---------------|------------------------------------|-----------------------
`+`            |追加                                 |`3.14 + 3.14`, `ago(5m) + 5m`
`-`            |減算                            |`0.23 - 0.22`,
`*`            |乗算                            |`1s * 5`, `2 * 2`
`/`            |除算                              |`10m / 1s`, `4 / 2`
`%`            |剰余                              |`4 % 2`
`<`            |未満                                |`1 < 10`, `10sec < 1h`, `now() < datetime(2100-01-01)`
`>`            |より大きい                             |`0.23 > 0.22`, `10min > 1sec`, `now() > ago(1d)`
`==`           |次の値と等しい                              |`1 == 1`
`!=`           |次の値と等しくない                          |`1 != 0`
`<=`           |以下                       |`4 <= 5`
`>=`           |以上                    |`5 >= 4`
`in`           |要素のいずれかに等しい       |[こちらをご覧ください](inoperator.md)
`!in`          |要素のいずれとも等しくない   |[こちらをご覧ください](inoperator.md)

> [!NOTE]
> ある数値型から別の数値型に変換するには、関数を使用し `to*()` ます。 たとえば、「」および「」を参照してください [`tolong()`](tolongfunction.md) [`toint()`](tointfunction.md) 。

**剰余演算子に関するコメント**

2つの数値の剰余は、Kusto は常に "小さい負以外の数値" に戻ります。
したがって、 *n* d という2つの数値の剰余  %  *D* は、0 &le; (*n*  %  *d*) &lt; abs (*D*) になります。

たとえば、次のクエリがあります。

```kusto
print plusPlus = 14 % 12, minusPlus = -14 % 12, plusMinus = 14 % -12, minusMinus = -14 % -12
```

では、次の結果が生成されます。

|plusPlus  | minusPlus  | plusMinus  | minusMinus|
|----------|------------|------------|-----------|
|2         | 10         | 2          | 10        |
