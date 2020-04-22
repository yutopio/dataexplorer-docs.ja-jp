---
title: prev() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで prev() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33f6045333826b21ddc0092e2cc7d5e033c12a96
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744603"
---
# <a name="prev"></a>prev()

[シリアル化](./windowsfunctions.md#serialized-row-set)された行セットの現在の行より前のオフセットにある列の値を返します。

**構文**

`prev(column)`

`prev(column, offset)`

`prev(column, offset, default_value)`

**引数**

* `column`: 値を取得する列。

* `offset`: 行に戻るオフセット。 オフセットが指定されていない場合、デフォルトのオフセット 1 が使用されます。

* `default_value`: 値を取得する前の行がない場合に使用される既定値。 既定値を指定しない場合は、null が使用されます。


**使用例**

```kusto
Table | serialize | extend prevA = prev(A,1)
| extend diff = A - prevA
| where diff > 1

Table | serialize prevA = prev(A,1,10)
| extend diff = A - prevA
| where diff <= 10
```