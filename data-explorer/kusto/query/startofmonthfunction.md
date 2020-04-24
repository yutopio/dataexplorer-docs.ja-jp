---
title: 開始月() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの開始の month() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e220919f6b09dbcd2519c67f48148a6375395e7b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507244"
---
# <a name="startofmonth"></a>startofmonth()

指定した場合、日付を含む月の開始位置をオフセットで返します。

**構文**

`startofmonth(`*日付*`,`[*オフセット*]`)`

**引数**

* `date`: 入力日付。
* `offset`: 入力日付からのオフセット月数 (整数、デフォルト - 0)。

**戻り値**

指定された*日付*値の月の開始日を表す日時。(オフセットが指定されている場合)。

**例**

```kusto
  range offset from -1 to 1 step 1
 | project monthStart = startofmonth(datetime(2017-01-01 10:10:17), offset) 
```

|月 スタート|
|---|
|2016-12-01 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-02-01 00:00:00.0000000|