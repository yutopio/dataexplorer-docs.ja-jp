---
title: binary_shift_left() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで binary_shift_left() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 15e2bee789e627709ccfedde8eccead7f2578b51
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517563"
---
# <a name="binary_shift_left"></a>binary_shift_left()

数値のペアに対する 2 項シフト左の演算を返します。

```kusto
binary_shift_left(x,y)  
```

**構文**

`binary_shift_left(`*num1* `,` *num2*`)`

**引数**

* *num1* *、num2*: int 番号。

**戻り値**

数値の組み合わせに対して 2 進シフト左の演算を返します: num1 << (num2%64)
n が負の値の場合は、NULL 値が返されます。