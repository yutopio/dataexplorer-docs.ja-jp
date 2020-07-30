---
title: binary_and ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの binary_and () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 611580aebbfd974377f5a22ec904bbbcdbeb6e3f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349093"
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