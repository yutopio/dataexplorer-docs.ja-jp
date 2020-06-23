---
title: totimespan ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの totimespan () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b785f346dd95a9c6a8cb9d6148e889c42ac4b02c
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2020
ms.locfileid: "85129041"
---
# <a name="totimespan"></a>totimespan()

入力を[timespan](./scalar-data-types/timespan.md)スカラーに変換します。

```kusto
totimespan("0.00:01:00") == time(1min)
```

**構文**

`totimespan(Expr)`

**引数**

* *`Expr`*: [Timespan](./scalar-data-types/timespan.md)に変換される式。

**戻り値**

変換が成功した場合、結果は[timespan](./scalar-data-types/timespan.md)値になります。
それ以外の場合、結果は null になります。
