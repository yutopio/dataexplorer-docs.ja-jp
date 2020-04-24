---
title: スタートオブウィーク() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの week() の開始について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9705b586a0b8c5161b7d4c27735f644b6982c707
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507227"
---
# <a name="startofweek"></a>startofweek()

指定した場合、日付を含む週の開始位置を返します。

週の始まりは日曜日と見なされます。

**構文**

`startofweek(`*日付*`,`[*オフセット*]`)`

**引数**

* `date`: 入力日付。
* `offset`: 入力日付からのオフセット週数 (整数、デフォルト - 0)。

**戻り値**

指定された*日付*値の週の開始日を表す日時。(オフセットが指定されている場合)。

**例**

```kusto
  range offset from -1 to 1 step 1
 | project weekStart = startofweek(datetime(2017-01-01 10:10:17), offset) 
```

|週スタート|
|---|
|2016-12-25 00:00:00.0000000|
|2017-01-01 00:00:00.0000000|
|2017-01-08 00:00:00.0000000|