---
title: tolong() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで tolong() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd354a39c048631ab98390c74cb7cd78ee5376b2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506088"
---
# <a name="tolong"></a>tolong()

入力を long (符号付き 64 ビット) 数値表現に変換します。

```kusto
tolong("123") == 123
```

**構文**

`tolong(`*Expr*`)`

**引数**

* *Expr*: 長整数型に変換される式。 

**戻り値**

変換が成功すると、結果は長い数値になります。
変換が成功しなかった場合は、 が`null`返されます。
 
*注意*: 可能な場合[は long() を](./scalar-data-types/long.md)使用することをお好みで