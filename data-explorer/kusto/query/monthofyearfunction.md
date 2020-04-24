---
title: 月次年() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの monthofyear() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6ada34d3e6f6c905a1acfb550b02af3dab550fb8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512344"
---
# <a name="monthofyear"></a>monthofyear()

整数を返します指定した年の月数を表します。

別のエイリアス: getmonth()

```kusto
monthofyear(datetime("2015-12-14"))
```

**構文**

`monthofyear(`*a_date*`)`

**引数**

* `a_date`: `datetime`。

**戻り値**

`month number`与えられた年の。