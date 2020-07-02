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
ms.openlocfilehash: f99406d94e1cd64da8605e5000aa99136c2b119a
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763781"
---
# <a name="tobool"></a>tobool()

入力をブール値 (符号付き8ビット) 表現に変換します。

```kusto
tobool("true") == true
tobool("false") == false
tobool(1) == true
tobool(123) == true
```

**構文**

`tobool(`*Expr* `)` 
 Expr `toboolean(`*Expr* `)`エイリアス

**引数**

* *Expr*: ブール型に変換される式。 

**戻り値**

変換が成功した場合、結果はブール値になります。
変換に失敗した場合、結果はになり `null` ます。
