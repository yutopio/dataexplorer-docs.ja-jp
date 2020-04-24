---
title: デイオブイヤー() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの dayofyear() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 41e7c5906da001e877dd9124124e126d729e886d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516220"
---
# <a name="dayofyear"></a>dayofyear()

整数を返し、指定した年の日の番号を表します。

```kusto
dayofyear(datetime(2015-12-14))
```

**構文**

`dayofweek(`*a_date*`)`

**引数**

* `a_date`: `datetime`。

**戻り値**

`day number`与えられた年の。