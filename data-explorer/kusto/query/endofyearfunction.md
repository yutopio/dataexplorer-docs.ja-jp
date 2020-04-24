---
title: 年末() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの endofyear() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d4a14d1cc42d5b9116e8a144e2b67fb74c1932ee
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515778"
---
# <a name="endofyear"></a>endofyear()

指定した場合、日付を含む年の終わりを返します。

**構文**

`endofyear(`*日付*`,`[*オフセット*]`)`

**引数**

* `date`: 入力日付。
* `offset`: 入力日付からのオフセット年数 (整数、デフォルト - 0)。

**戻り値**

指定された*日付*値の年の終わりを表す日時。(オフセットが指定されている場合)。

**例**

```kusto
  range offset from -1 to 1 step 1
 | project yearEnd = endofyear(datetime(2017-01-01 10:10:17), offset) 
```

|年末|
|---|
|2016-12-31 23:59:59.9999999|
|2017-12-31 23:59:59.9999999|
|2018-12-31 23:59:59.9999999|