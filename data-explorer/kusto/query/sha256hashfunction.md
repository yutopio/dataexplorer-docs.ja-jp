---
title: hash_sha256 ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの hash_sha256 () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 32fa2f3ffefdbf1f14ed87e8e89444de322408c3
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372316"
---
# <a name="hash_sha256"></a>hash_sha256()

入力値の sha256 ハッシュ値を返します。

**構文**

`hash_sha256(`*電源*`)`

**引数**

* *source*: ハッシュされる値。

**戻り値**

16進数文字列としてエンコードされた、指定されたスカラーの sha256 ハッシュ値 (文字の文字列。それぞれが 0 ~ 255 の範囲の1つの16進数を表します)。

> [!WARNING]
> この関数 (SHA256) で使用されるアルゴリズムは、今後変更されることはありませんが、計算が非常に複雑です。 1つのクエリの実行中に "ライトウェイト" ハッシュ関数を必要とするユーザーには、代わりに関数[hash ()](./hashfunction.md)を使用することをお勧めします。

**使用例**

```kusto
hash_sha256("World")                   // 78ae647dc5544d227130a0682a51e30bc7777fbb6d8a8f17007463a3ecd1d524
hash_sha256(datetime("2015-01-01"))    // e7ef5635e188f5a36fafd3557d382bbd00f699bd22c671c3dea6d071eb59fbf8
```

次の例では、hash_sha256 関数を使用して、データの StartTime 列に対してクエリを実行します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where hash_sha256(StartTime) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```
