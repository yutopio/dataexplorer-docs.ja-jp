---
title: hash_combine ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの hash_combine () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: 1925d9b27382dd3a888e14243bfecad51d37db0d
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226705"
---
# <a name="hash_combine"></a>hash_combine()

2つ以上のハッシュのハッシュ値を結合します。

**構文**

`hash_combine(`*h1* `,`*h2* [ `,` *h3* ...]`)`

**引数**

* *h1*: 最初のハッシュ値を表す Long 値。
* *h2*: 2 番目のハッシュ値を表す Long 型の値です。
* *hN*: n 番目のハッシュ値を表す Long 型の値。

**戻り値**

指定されたスカラーの結合ハッシュ値。

**使用例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend h1 = hash(value1), h2=hash(value2)
| extend combined = hash_combine(h1, h2)
```

|value1|value2|h1|h2|さ|
|---|---|---|---|---|
|こんにちは|World|753694413698530628|1846988464401551951|-1440138333540407281|
