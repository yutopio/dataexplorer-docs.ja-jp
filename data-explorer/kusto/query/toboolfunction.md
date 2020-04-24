---
title: tobool() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで tobool() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3e1867c99ae241b3f7e09ab8ee873d5ae5d374e0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506360"
---
# <a name="tobool"></a>tobool()

入力をブール (符号付き 8 ビット) 表現に変換します。

```kusto
tobool("true") == true
tobool("false") == false
tobool(1) == true
tobool(123) == true
```

**構文**

`tobool(`*エクス*`)`
プル`toboolean(`*エクスプ*`)`

**引数**

* *Expr*: ブール値に変換される式。 

**戻り値**

変換が成功すると、結果はブール値になります。
変換が成功しなかった場合は、 が`null`返されます。
 