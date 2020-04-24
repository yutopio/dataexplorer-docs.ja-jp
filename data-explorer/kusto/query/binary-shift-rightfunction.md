---
title: binary_shift_right() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで binary_shift_right() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a94c8c695f1c5d16ee0a7e3a92882486b8a8ef5d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517546"
---
# <a name="binary_shift_right"></a>binary_shift_right()

数値のペアに対する 2 進シフト右の演算を返します。

```kusto
binary_shift_right(x,y) 
```

**構文**

`binary_shift_right(`*num1* `,` *num2*`)`

**引数**

* *num1* *、num2*: 長い数値。

**戻り値**

数値のペアに対する 2 進シフト右の演算を返します: num1 >>  (num2%64)。
n が負の値の場合は、NULL 値が返されます。