---
title: 終了日() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの endofday() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a25f6b9c4ba660fbb8a50390d9d6920ff21caeb8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515880"
---
# <a name="endofday"></a>endofday()

指定した場合、日付を含む日の終わりをオフセットで返します。

**構文**

`endofday(`*日付*`,`[*オフセット*]`)`

**引数**

* `date`: 入力日付。
* `offset`: 入力日付からのオフセット日数 (整数、デフォルト - 0)

**戻り値**

指定された*日付*値の日付の終わりを表す日時。(オフセットが指定されている場合)。

**例**

```kusto
  range offset from -1 to 1 step 1
 | project dayEnd = endofday(datetime(2017-01-01 10:10:17), offset) 
```

|デイエンド|
|---|
|2016-12-31 23:59:59.9999999|
|2017-01-01 23:59:59.9999999|
|2017-01-02 23:59:59.9999999|