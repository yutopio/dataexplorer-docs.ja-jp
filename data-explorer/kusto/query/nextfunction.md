---
title: 次の() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの next() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f45e88942fdf9eb23451e1391866f57ca5f0e21a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512123"
---
# <a name="next"></a>next()

[シリアル化](./windowsfunctions.md#serialized-row-set)された行セットの現在の行の後にあるオフセットの行の値を返します。

**構文**

`next(column)`

`next(column, offset)`

`next(column, offset, default_value)`

**引数**

* `column`: 値を取得する列。

* `offset`: 行先に進むオフセット。 オフセットが指定されていない場合、デフォルトのオフセット 1 が使用されます。

* `default_value`: 値を取得する次の行がない場合に使用される既定値。 既定値を指定しない場合は、null が使用されます。


**使用例**
```kusto
Table | serialize | extend nextA = next(A,1)
| extend diff = A - nextA
| where diff > 1

Table | serialize nextA = next(A,1,10)
| extend diff = A - nextA
| where diff <= 10
```