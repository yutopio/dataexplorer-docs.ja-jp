---
title: gettype() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで gettype() を説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 3a28032320948f12b2f91febc9f59c7b35ad084e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514384"
---
# <a name="gettype"></a>gettype()

単一引数の実行時の型を返します。

ランタイム型は、名型が 、 である式の名義型 (静的`dynamic`) 型とは異なる場合があります。このような場合`gettype()`は、実際の値の t 型 (値がメモリでどのようにエンコードされているか) を明らかにするのに役立ちます。

**構文**

`gettype(`*Expr*`)`

**戻り値**

単一引数のランタイム型を表す文字列。

**使用例**

|式                          |戻り値      |
|------------------------------------|-------------|
|`gettype("a")`                      |`string`     |
|`gettype(111)`                      |`long`       |
|`gettype(1==1)`                     |`bool`       |
|`gettype(now())`                    |`datetime`   |
|`gettype(1s)`                       |`timespan`   |
|`gettype(parse_json('1'))`           |`int`        |
|`gettype(parse_json(' "abc" '))`     |`string`     |
|`gettype(parse_json(' {"abc":1} '))` |`dictionary` | 
|`gettype(parse_json(' [1, 2, 3] '))` |`array`      |
|`gettype(123.45)`                   |`real`       |
|`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))`|`guid`| 
|`gettype(parse_json(''))`            |`null`|