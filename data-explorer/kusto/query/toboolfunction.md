---
title: tobool ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの tobool () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e0343ae5cb98e1cb3114e24c963fe2981be82c5b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350793"
---
# <a name="tobool"></a>tobool()

入力をブール値 (符号付き8ビット) 表現に変換します。

```kusto
tobool("true") == true
tobool("false") == false
tobool(1) == true
tobool(123) == true
```

## <a name="syntax"></a>構文

`tobool(`*Expr* `)` 
 Expr `toboolean(`*Expr* `)`エイリアス

## <a name="arguments"></a>引数

* *Expr*: ブール型に変換される式。 

## <a name="returns"></a>戻り値

変換が成功した場合、結果はブール値になります。
変換に失敗した場合、結果はになり `null` ます。
