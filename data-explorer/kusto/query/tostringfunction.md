---
title: tostring ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの tostring () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 634f54533e83575139d8399124cc068af56d8574
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741669"
---
# <a name="tostring"></a>tostring()

入力を文字列形式に変換します。

```kusto
tostring(123) == "123"
```

**構文**

`tostring(`*`Expr`*`)`

**引数**

* *`Expr`*: 文字列に変換される式。 

**戻り値**

*`Expr`* 値が null 以外の場合、結果はの*`Expr`* 文字列表現になります。
*`Expr`* 値が null の場合、結果は空の文字列になります。
 