---
title: isinf() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで isinf() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d93697890ee05cabf9ca1830ac047d90d8c9e844
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513585"
---
# <a name="isinf"></a>isinf()

入力が無限 (正または負) の値かどうかを返します。  

**構文**

`isinf(`*X*`)`

**引数**

* *x*: 実数。

**戻り値**

x が正または負の無限大の場合は、ゼロ以外の値 (true) を返します。それ以外の場合はゼロ (false) です。

**参照**

* 値が null かどうかを調べるには[、isnull()](isnullfunction.md)を参照してください。
* 値が有限かどうかの検査については[、isfinite()](isfinitefunction.md)を参照してください。
* 値が NaN (非番号) であるかどうかを確認する場合は[、isnan()](isnanfunction.md)を参照してください。

**例**

```kusto
range x from -1 to 1 step 1
| extend y = 0.0
| extend div = 1.0*x/y
| extend isinf=isinf(div)
```

|x|y|div|isinf|
|---|---|---|---|
|-1|0|-∞|1|
|0|0|(NaN)|0|
|1|0|∞|1|
