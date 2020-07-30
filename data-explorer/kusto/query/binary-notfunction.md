---
title: binary_not ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの binary_not () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b0672652836edce82be0fc13cd17d6d5d6fe5b62
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349076"
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