---
title: デイオブマンス() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの dayof month() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 791d85d4a8f89487e65ef68ecc605f907e63e1ea
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516339"
---
# <a name="dayofmonth"></a>dayofmonth()

指定した月の日数を表す整数を返します。

```kusto
dayofmonth(datetime(2015-12-14)) == 14
```

**構文**

`dayofmonth(`*a_date*`)`

**引数**

* `a_date`: `datetime`。

**戻り値**

`day number`与えられた月の。