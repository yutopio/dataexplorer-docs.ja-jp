---
title: hash() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで hash() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f8142c42dcb0874dfbd84515e56dc8765bcba3d7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514146"
---
# <a name="hash"></a>hash()

入力値のハッシュ値を返します。

**構文**

`hash(`*ソース*`,` [*モッズ*]`)`

**引数**

* *source*: ハッシュされる値。
* *mod*: ハッシュ結果に適用されるオプションのモジュール値で、出力値が mod`0`と*mod*の間にあるように - 1

**戻り値**

指定されたスカラーのハッシュ値(指定されている場合)は、指定されたmod値をモジュロします。

> [!WARNING]
> ハッシュの計算に使用されるアルゴリズムは xxhash です。
> このアルゴリズムは将来変更される可能性があり、単一のクエリ内でこのメソッドのすべての呼び出しで同じアルゴリズムが使用されるという保証があります。
> したがって、ユーザーは、テーブルに結果`hash()`を格納しないことをお勧めします。 ハッシュ値を永続化する必要がある場合は、[代わりに hash_sha256()](./sha256hashfunction.md)を使用することを検討`hash()`してください (ただし、計算する方がはるかに複雑であることに注意してください)。

**使用例**

```kusto
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```

次の例では、ハッシュ関数を使用してデータの 10% に対してクエリを実行しますが、値が均一に分散されていると仮定した場合に、データをサンプリングするためにハッシュ関数を使用すると便利です (この例では StartTime 値)。

```kusto
StormEvents 
| where hash(StartTime, 10) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```