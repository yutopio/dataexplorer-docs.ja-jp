---
title: binary_shift_left ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの binary_shift_left () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 46d18bb5d1f661c5346f5ff825c9597088d3f5cf
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349042"
---
# <a name="binary_shift_left"></a>binary_shift_left()

数値のペアに対してバイナリシフトの左操作を返します。

```kusto
binary_shift_left(x,y)  
```

## <a name="syntax"></a>構文

`binary_shift_left(`*num1* `,`*num2*`)`

## <a name="arguments"></a>引数

* *num1*, *num2*: int number.

## <a name="returns"></a>戻り値

2つの数値のペアに対してバイナリシフトの左操作を返します: num1 <<  (num2% 64)。
N が負の値の場合は、NULL 値が返されます。