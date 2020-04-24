---
title: bitset_count_ones() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの bitset_count_ones() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: c23e95ee9f00a0ca173d68d3591ad604b31dfac2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517393"
---
# <a name="bitset_count_ones"></a>bitset_count_ones()

数値のバイナリ表現で設定されたビット数を返します。

```kusto
bitset_count_ones(42)
```

**構文**

`bitset_count_ones(`*num1*''' '

**引数**

* *num1*: 長整数または整数。

**戻り値**

数値のバイナリ表現で設定されたビット数を返します。

**例**

```kusto
// 42 = 32+8+2 : b'00101010' == 3 bits set
print ones = bitset_count_ones(42) 
```

|もの|
|---|
|3|