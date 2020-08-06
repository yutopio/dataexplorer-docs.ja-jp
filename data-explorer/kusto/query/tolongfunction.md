---
title: tolong)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの tolong) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 36c0317f046f146d2812b8830d7fe571d5363c59
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804119"
---
# <a name="tolong"></a>tolong()

入力を long (符号付き64ビット) 数値表現に変換します。

```kusto
tolong("123") == 123
```

> [!NOTE]
> 可能であれ[ば、long ()](./scalar-data-types/long.md)を使用することをお勧めします。

## <a name="syntax"></a>構文

`tolong(`*With*`)`

## <a name="arguments"></a>引数

* *Expr*: long 型に変換される式。 

## <a name="returns"></a>戻り値

変換が成功した場合、結果は長い数値になります。
変換に失敗した場合、結果はになり `null` ます。
 