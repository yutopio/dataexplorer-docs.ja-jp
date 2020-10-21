---
title: tobool ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの tobool () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e7fa3d12b1dfc1fcbede2f5f47b3841102b72e5a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250587"
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
