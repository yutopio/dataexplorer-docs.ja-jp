---
title: オーバーフロー - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのオーバーフローについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 22db905788e1313cad2eb229239a390c28bcd5c2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523156"
---
# <a name="overflows"></a>オーバーフロー

計算の結果が変換先の型に対して大きすぎると、オーバーフローが発生します。
この現象は、通常、[部分的なクエリエラー](partialqueryfailures.md)につながります。

たとえば、次のクエリはオーバーフローを発生させます。

```kusto
let Weight = 92233720368547758;
range x from 1 to 3 step 1
| summarize percentilesw(x, Weight * 100, 50)
```

Kusto の`percentilesw()`実装では、「十`Weight`分に近い」値の式が蓄積されます。
この場合、累積は符号付き 64 ビット整数に収まらないため、オーバーフローをトリガーします。

ただし、通常、Kusto は 64 ビット型を算術計算に使用するため、オーバーフローはクエリの "バグ" の結果です。
このような場合の最善のアクションは、オーバーフローを引き起こした関数または集計をエラー メッセージから識別し、その入力引数が意味のある値に評価されるようにすることです。
