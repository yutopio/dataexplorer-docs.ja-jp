---
title: hash_sha256() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで hash_sha256() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2147f4e9f2bd3d7df8f75ac704a4e4808e69bb3c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507601"
---
# <a name="hash_sha256"></a>hash_sha256()

入力値の sha256 ハッシュ値を返します。

**構文**

`hash_sha256(`*ソース*`)`

**引数**

* *source*: ハッシュされる値。

**戻り値**

16 進文字列としてエンコードされた、指定されたスカラーの sha256 ハッシュ値 (文字列で、それぞれ 0 ~ 255 の 1 つの 16 進数を表します)。

> [!WARNING]
> この関数(SHA256)で使用されるアルゴリズムは、将来変更されないことが保証されていますが、計算は非常に複雑です。 1 つのクエリの実行中に "軽量" ハッシュ関数が必要なユーザーは、代わりに関数[hash()](./hashfunction.md)を使用することをお勧めします。

**使用例**

```kusto
hash_sha256("World")                   // 78ae647dc5544d227130a0682a51e30bc7777fbb6d8a8f17007463a3ecd1d524
hash_sha256(datetime("2015-01-01"))    // e7ef5635e188f5a36fafd3557d382bbd00f699bd22c671c3dea6d071eb59fbf8
```

次の例では、hash_sha256関数を使用して、データの StartTime 列に対してクエリを実行します。

```kusto
StormEvents 
| where hash_sha256(StartTime) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```