---
title: dayofweek ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの dayofweek () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4e445a86b976f251de2beef4726c4840bcec8e44
ms.sourcegitcommit: ee90472a4f9d751d4049744d30e5082029c1b8fa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722033"
---
# <a name="dayofweek"></a>dayofweek()

前の日曜日からの日数を表す整数を返し `timespan` ます。

```kusto
dayofweek(datetime(2015-12-14)) == 1d  // Monday
```

**構文**

`dayofweek(`*a_date*`)`

**引数**

* `a_date`:`datetime`。

**戻り値**

前の日曜日の午前 0 時からの `timespan`。日数を示す整数に切り捨てられます。

**使用例**

```kusto
dayofweek(datetime(1947-11-30 10:00:05))  // time(0.00:00:00), indicating Sunday
dayofweek(datetime(1970-05-11))           // time(1.00:00:00), indicating Monday
```
