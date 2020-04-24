---
title: iff() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで iff() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d5eb8af5fd98fb24c677390c314d112e5634cacf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514061"
---
# <a name="iff"></a>iff()

最初の引数 (述語) を評価し、2 番目または 3 番目の引数の値を`true``false`返します。

2 番目と 3 番目の引数は、同じ型である必要があります。

**構文**

`iff(`*述語*`,`*の場合 True* `,` *の場合False*`)`

**引数**

* *述語*:`boolean`値に評価される式。
* *ifTrue*: 評価を受け取る式で、*述語*が に評価`true`された場合に関数から返される値です。
* *ifFalse*: 評価を受け取る式で、*述語*が に評価`false`された場合に関数から返される値です。

**戻り値**

この関数は、*predicate* が `true` と評価された場合に *ifTrue* の値を返し、それ以外の場合に *ifFalse* の値を返します。

**例**

```kusto
T 
| extend day = iff(floor(Timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```