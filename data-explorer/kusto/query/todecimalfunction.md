---
title: todecimal() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの todecimal() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2d5ac70dfe71f80c3963292516e1b8516a297875
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506309"
---
# <a name="todecimal"></a>todecimal()

入力を 10 進数表現に変換します。

```kusto
todecimal("123.45678") == decimal(123.45678)
```

**構文**

`todecimal(`*Expr*`)`

**引数**

* *Expr*: 10 進数に変換される式。 

**戻り値**

変換が成功すると、結果は 10 進数になります。
変換が成功しなかった場合は、 が`null`返されます。
 
*注意*: 可能な場合[は real()](./scalar-data-types/real.md)を使用することを優先します。