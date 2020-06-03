---
title: オーバーフロー-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのオーバーフローについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 063165c91319ef5f4183a8ce8f83364fd8188072
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84328961"
---
# <a name="overflows"></a>オーバーフロー

オーバーフローは、計算の結果が変換先の型に対して大きすぎる場合に発生します。
オーバーフローは通常、[部分的なクエリの失敗](partialqueryfailures.md)につながります。

たとえば、次のクエリではオーバーフローが発生します。

```kusto
let Weight = 92233720368547758;
range x from 1 to 3 step 1
| summarize percentilesw(x, Weight * 100, 50)
```

Kusto の `percentilesw()` 実装は、 `Weight` "十分に近い" 値の式を累積します。
この場合、累積は、符号付き64ビット整数に収まらないため、オーバーフローをトリガーします。

通常、Kusto では算術計算に64ビット型が使用されるため、オーバーフローはクエリの "バグ" の結果となります。
最良の措置は、エラーメッセージを確認し、オーバーフローをトリガーした関数または集計を特定することです。 入力引数が意味のある値に評価されていることを確認します。
