---
title: binary_or ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの binary_or () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6903ee36e29551e7af6d08e686c1189e0c0125f3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349059"
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