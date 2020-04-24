---
title: binary_and() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの binary_and() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8c5501218c1ddb69a73f685fda78f3b3482afb5c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517682"
---
# <a name="binary_and"></a>binary_and()

2 つの値間のビット`and`ごとの演算の結果を返します。

```kusto
binary_and(x,y) 
```

**構文**

`binary_and(`*num1* `,` *num2*`)`

**引数**

* *num1* *、num2*: 長い数値。

**戻り値**

数値の組み合わせで論理 AND 演算を返します: num1 & num2 です。