---
title: binary_or ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの binary_or () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 97144b244fb6fea5ac218f6160d8aa9e95f50aa4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247892"
---
# <a name="binary_or"></a>binary_or()

2つの値のビットごとの演算の結果を返し `or` ます。 

```kusto
binary_or(x,y)
```

## <a name="syntax"></a>構文

`binary_or(`*num1* `,`*num2*`)`

## <a name="arguments"></a>引数

* *num1*, *num2*: long 数値。

## <a name="returns"></a>戻り値

数値のペアに対して論理 OR 演算を返します: num1 |num2.