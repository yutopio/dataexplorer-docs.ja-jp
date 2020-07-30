---
title: todecimal ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの todecimal () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f699508436c9e2533661a440be2ac8f5f8d94688
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350776"
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
 
*注*: 可能な場合は、 [real ()](./scalar-data-types/real.md)を使用することをお勧めします。