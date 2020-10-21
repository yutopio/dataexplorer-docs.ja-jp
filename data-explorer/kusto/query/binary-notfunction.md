---
title: binary_not ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの binary_not () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e514f8ce7ee53262c1ef82b650c7ad620151a689
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243534"
---
# <a name="binary_not"></a>binary_not()

入力値のビットごとの否定を返します。

```kusto
binary_not(x)
```

## <a name="syntax"></a>構文

`binary_not(`*ターン*`)`

## <a name="arguments"></a>引数

* *num1*: numeric 

## <a name="returns"></a>戻り値

数値に対する論理 NOT 演算を返します: num1。