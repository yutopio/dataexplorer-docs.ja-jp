---
title: isascii() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの isascii() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: daba0f4015a4847155309964f8ac0909ff4bc9d0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513721"
---
# <a name="isascii"></a>isascii()

引数`true`が有効な ascii 文字列の場合に返します。
    
```kusto
isascii("some string") == true
```

**構文**

`isascii(`[*値*]`)`

**戻り値**

引数が有効な ascii 文字列であるかどうかを示します。

**例**

```kusto
T
| where isascii(fieldName)
| count
```