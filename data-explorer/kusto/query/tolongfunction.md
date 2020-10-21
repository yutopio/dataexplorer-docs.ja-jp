---
title: tolong)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの tolong) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bf4960ae3bd11697e4e7203438e1a33af6c90672
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92240996"
---
# <a name="tolong"></a>tolong()

入力を long (符号付き64ビット) 数値表現に変換します。

```kusto
tolong("123") == 123
```

> [!NOTE]
> 可能であれ [ば、long ()](./scalar-data-types/long.md) を使用することをお勧めします。

## <a name="syntax"></a>構文

`tolong(`*With*`)`

## <a name="arguments"></a>引数

* *Expr*: long 型に変換される式。 

## <a name="returns"></a>戻り値

変換が成功した場合、結果は長い数値になります。
変換に失敗した場合、結果はになり `null` ます。
 