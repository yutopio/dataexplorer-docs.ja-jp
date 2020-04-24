---
title: isfinite() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの isfinite() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a17a39cce91fe039b2cf55cc5c98dba111cc334
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513602"
---
# <a name="isfinite"></a>isfinite()

入力が有限値 (無限でも NaN でもない) かどうかを返します。

**構文**

`isfinite(`*X*`)`

**引数**

* *x*: 実数。

**戻り値**

x が有限の場合は 0 以外の値 (true) です。それ以外の場合はゼロ (false) です。

**参照**

* 値が null かどうかを調べるには[、isnull()](isnullfunction.md)を参照してください。
* 値が無限であるかどうかを確認するには[、isinf()](isinffunction.md)を参照してください。
* 値が NaN (非番号) であるかどうかを確認する場合は[、isnan()](isnanfunction.md)を参照してください。

**例**

```kusto
range x from -1 to 1 step 1
| extend y = 0.0
| extend div = 1.0*x/y
| extend isfinite=isfinite(div)
```

|x|y|div|イスフィント|
|---|---|---|---|
|-1|0|-∞|0|
|0|0|(NaN)|0|
|1|0|∞|0|