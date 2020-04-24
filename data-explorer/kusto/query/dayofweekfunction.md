---
title: デイオブウィーク() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの dayofweek() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 31d3f525653f6e0979229e4355cdec6cb76833f8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516305"
---
# <a name="dayofweek"></a>dayofweek()

前の日曜日以降の整数の日数を返します`timespan`。

```kusto
dayofweek(datetime(2015-12-14)) == 1d  // Monday
```

**構文**

`dayofweek(`*a_date*`)`

**引数**

* `a_date`: `datetime`。

**戻り値**

前の日曜日の午前 0 時からの `timespan`。日数を示す整数に切り捨てられます。

**使用例**

```kusto
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```