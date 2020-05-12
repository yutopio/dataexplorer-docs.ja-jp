---
title: hash_many ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの hash_many () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: 66ca1e5ff330a4b39ab769b0e3e8d6359eed9c00
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226671"
---
# <a name="hash_many"></a>hash_many()

複数の値の結合されたハッシュ値を返します。

**構文**

`hash_many(`*s1* `,`*s2* [ `,` *s3* ...]`)`

**引数**

* *s1*、 *s2*、...、 *sN*: 入力値がまとめてハッシュされます。

**戻り値**

指定されたスカラーの結合ハッシュ値。

**使用例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend combined = hash_many(value1, value2)
```

|value1|value2|さ|
|---|---|---|
|こんにちは|World|-1440138333540407281|
