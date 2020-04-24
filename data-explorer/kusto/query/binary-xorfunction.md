---
title: binary_xor() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでbinary_xor() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c2f487aa44f8885cbb443c31b8bb3a503e1a76fa
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517529"
---
# <a name="binary_xor"></a>binary_xor()

2 つの値のビットごとの`xor`演算の結果を返します。

```kusto
binary_xor(x,y)
```

**構文**

`binary_xor(`*num1* `,` *num2*`)`

**引数**

* *num1* *、num2*: 長い数値。

**戻り値**

数値の組み合わせに対する論理 XOR 演算を返します: num1 ^ num2。