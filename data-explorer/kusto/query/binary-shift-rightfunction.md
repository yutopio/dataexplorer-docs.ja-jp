---
title: binary_shift_right ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの binary_shift_right () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 96da8894aa4320a2d423d072acc048994463a7b3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349025"
---
# <a name="binary_shift_right"></a>binary_shift_right()

数値のペアに対して、バイナリシフトの右操作を返します。

```kusto
binary_shift_right(x,y) 
```

## <a name="syntax"></a>構文

`binary_shift_right(`*num1* `,`*num2*`)`

## <a name="arguments"></a>引数

* *num1*, *num2*: long 数値。

## <a name="returns"></a>戻り値

2つの数値のペアに対してバイナリシフトの右操作を返します: num1 >>  (num2% 64)。
N が負の値の場合は、NULL 値が返されます。