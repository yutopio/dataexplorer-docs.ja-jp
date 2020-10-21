---
title: todecimal ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの todecimal () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d4b9f020a1d37b7279c0712951ed0c2e39e9e428
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250503"
---
# <a name="todecimal"></a>todecimal()

入力を10進数表現に変換します。

```kusto
todecimal("123.45678") == decimal(123.45678)
```

## <a name="syntax"></a>構文

`todecimal(`*With*`)`

## <a name="arguments"></a>引数

* *Expr*: decimal に変換される式。 

## <a name="returns"></a>戻り値

変換が成功した場合、結果は10進数になります。
変換に失敗した場合、結果はになり `null` ます。
 
*注*: 可能な場合は、 [real ()](./scalar-data-types/real.md) を使用することをお勧めします。