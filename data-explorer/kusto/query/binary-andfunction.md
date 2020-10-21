---
title: binary_and ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの binary_and () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 93318785cff98c7ca024b638e5e90f58cd9cd9d6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249363"
---
# <a name="binary_and"></a>binary_and()

2つの値の間のビットごとの演算の結果を返し `and` ます。

```kusto
binary_and(x,y) 
```

## <a name="syntax"></a>構文

`binary_and(`*num1* `,`*num2*`)`

## <a name="arguments"></a>引数

* *num1*, *num2*: long 数値。

## <a name="returns"></a>戻り値

数値ペアの論理 AND 演算を返します: num1 & num2。