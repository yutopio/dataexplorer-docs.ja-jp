---
title: isutf8() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで isutf8() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 619fb90b72fed8ec0e10fe05ddc3c6df6ff1386e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513398"
---
# <a name="isutf8"></a>isutf8()

引数`true`が有効な utf8 文字列かどうかを返します。
    
```kusto
isutf8("some string") == true
```

**構文**

`isutf8(`[*値*]`)`

**戻り値**

引数が有効な utf8 文字列であるかどうかを示します。

**例**

```kusto
T
| where isutf8(fieldName)
| count
```