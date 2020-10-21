---
title: bitset_count_ones ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの bitset_count_ones () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: fb14ffa5807a61ade913631c7d6da6b23edc26d9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245333"
---
# <a name="bitset_count_ones"></a>bitset_count_ones()

数値のバイナリ表現で設定されたビット数を返します。

```kusto
bitset_count_ones(42)
```

## <a name="syntax"></a>構文

`bitset_count_ones(`*num1*' ') '

## <a name="arguments"></a>引数

* *num1*: long または整数の数値。

## <a name="returns"></a>戻り値

数値のバイナリ表現で設定されたビット数を返します。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// 42 = 32+8+2 : b'00101010' == 3 bits set
print ones = bitset_count_ones(42) 
```

|思い|
|---|
|3|
