---
title: totimespan() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの totimespan() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 504d4a74e8c1b58a8a97fd80d6c846fcf7e3f527
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505867"
---
# <a name="totimespan"></a>totimespan()

入力を[タイムスパン](./scalar-data-types/timespan.md)スカラーに変換します。

```kusto
totimespan("0.00:01:00") == time(1min)
```

**構文**

`totimespan(`*Expr*`)`

**引数**

* *Expr*:[タイムスパン](./scalar-data-types/timespan.md)に変換される式 。 

**戻り値**

変換が成功すると、結果は[タイムスパン](./scalar-data-types/timespan.md)値になります。
変換が成功しなかった場合、結果は null になります。
 