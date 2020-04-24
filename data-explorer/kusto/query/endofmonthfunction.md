---
title: 月末日() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの月末日の月末について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ebab067c730e3cd61c84ae33eba4e49d7f0b0c0c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515863"
---
# <a name="endofmonth"></a>endofmonth()

指定した場合、日付を含む月末をオフセットで返します。

**構文**

`endofmonth(`*日付*`,`[*オフセット*]`)`

**引数**

* `date`: 入力日付。
* `offset`: 入力日付からのオフセット月数 (整数、デフォルト - 0)。

**戻り値**

指定された*日付*値の月末を表す日時。(オフセットが指定されている場合)。

**例**

```kusto
  range offset from -1 to 1 step 1
 | project monthEnd = endofmonth(datetime(2017-01-01 10:10:17), offset) 
```

|月の終わり|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-31 23:59:59.9999999|
|2017-02-28 23:59:59.9999999|