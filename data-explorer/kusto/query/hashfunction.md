---
title: hash ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの hash () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a44f817ea57a114400f45e9ca2a841150b4590a6
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226807"
---
# <a name="hash"></a>hash()

入力値のハッシュ値を返します。

**構文**

`hash(`*ソース*[ `,` *mod*]`)`

**引数**

* *source*: ハッシュされる値。
* *mod*: ハッシュの結果に適用されるオプションのモジュール値。出力値が `0` と*mod* -1 の間になります。

**戻り値**

指定した mod 値をモジュロした、指定したスカラーのハッシュ値 (指定されている場合)。

> [!WARNING]
> ハッシュの計算に使用されるアルゴリズムは、「」です。
> このアルゴリズムは将来変更される可能性があります。唯一の保証は、1つのクエリ内で、このメソッドのすべての呼び出しで同じアルゴリズムを使用することです。
> その結果、の結果をテーブルに格納しないようにすることをお勧めし `hash()` ます。 ハッシュ値を保持する必要がある場合は、代わりに[hash_sha256 ()](./sha256hashfunction.md)を使用することを検討してください (ただし、よりも計算がはるかに複雑になることに注意してください `hash()` )。

**使用例**

```kusto
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```

次の例では、ハッシュ関数を使用して、10% のデータに対してクエリを実行します。値が一様に分布していると仮定してデータをサンプリングするには、ハッシュ関数を使用すると便利です (この例では StartTime 値)。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where hash(StartTime, 10) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```
