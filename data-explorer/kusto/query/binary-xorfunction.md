---
title: binary_xor ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの binary_xor () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6f988fa3d14dab3188bf96825615972995291655
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349008"
---
# <a name="binary_xor"></a>binary_xor()

2つの値のビットごとの演算の結果を返し `xor` ます。

```kusto
binary_xor(x,y)
```

## <a name="syntax"></a>構文

`binary_xor(`*num1* `,`*num2*`)`

## <a name="arguments"></a>引数

* *num1*, *num2*: long 数値。

## <a name="returns"></a>戻り値

数値のペアに対して論理 XOR 演算を返します: num1 ^ num2。