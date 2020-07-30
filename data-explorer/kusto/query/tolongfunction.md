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
ms.openlocfilehash: fb209ff3784f6e24f184b576a1e8f94c52834ba4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340957"
---
# <a name="tolong"></a>tolong()

入力を long (符号付き64ビット) 数値表現に変換します。

```kusto
tolong("123") == 123
```

## <a name="syntax"></a>構文

`tolong(`*With*`)`

## <a name="arguments"></a>引数

* *Expr*: long 型に変換される式。 

## <a name="returns"></a>戻り値

変換が成功した場合、結果は長い数値になります。
変換に失敗した場合、結果はになり `null` ます。
 
*注*: 可能であれ[ば、long ()](./scalar-data-types/long.md)を使用することをお勧めします。