---
title: make_timespan() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでmake_timespan() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 31301f684ea700cf89e9234c4c43adab068319b7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512769"
---
# <a name="make_timespan"></a>make_timespan()

指定した[期間からタイムスパン](./scalar-data-types/timespan.md)スカラー値を作成します。

```kusto
make_timespan(1,12,30,55.123) == time(1.12:30:55.123)
```

**構文**

`make_timespan(`*時間*,*分*`)`

`make_timespan(`*時*,*分 ,**秒*`)`

`make_timespan(`*日*,*時*,*分*,*秒*`)`

**引数**

* *日*: 日 (正の整数値)
* *時*: 時 (0 から 23 までの整数値)
* *分*: 分 (0 から 59 までの整数値)
* *2 番目*の場合 (0 から 59.9999999 までの実数値)

**戻り値**

作成が成功すると、結果は[タイムスパン](./scalar-data-types/timespan.md)値になり、それ以外の場合は結果が null になります。
 
**例**

```kusto
print ['timespan'] = make_timespan(1,12,30,55.123)

```

|TimeSpan|
|---|
|1.12:30:55.1230000|


