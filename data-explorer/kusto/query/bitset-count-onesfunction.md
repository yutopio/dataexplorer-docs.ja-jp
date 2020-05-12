---
title: bitset_count_ones ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの bitset_count_ones () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: f8abb1683a2f15f012e9a9271681688c19901af0
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227599"
---
# <a name="bitset_count_ones"></a>bitset_count_ones()

数値のバイナリ表現で設定されたビット数を返します。

```kusto
bitset_count_ones(42)
```

**構文**

`bitset_count_ones(`*num1*' ') '

**引数**

* *num1*: long または整数の数値。

**戻り値**

数値のバイナリ表現で設定されたビット数を返します。

**例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// 42 = 32+8+2 : b'00101010' == 3 bits set
print ones = bitset_count_ones(42) 
```

|思い|
|---|
|3|
