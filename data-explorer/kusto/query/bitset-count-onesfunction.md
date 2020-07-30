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
ms.openlocfilehash: b8ebef923d1cc67c118317680e1ec414900a469e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348957"
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
