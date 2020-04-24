---
title: datetime_diff() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで datetime_diff() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fd62e27ac4f9ef0ec813a311ddb2b16f0a6c9a65
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516441"
---
# <a name="datetime_diff"></a>datetime_diff()

2 つの日時値の間のカレンダーの差[を](./scalar-data-types/datetime.md)計算します。

**構文**

`datetime_diff(`*期間*`,`*datetime_1datetime_2*`,`*datetime_2*`)`

**引数**

* `period`: `string`. 
* `datetime_1`:[日時](./scalar-data-types/datetime.md)値。
* `datetime_2`:[日時](./scalar-data-types/datetime.md)値。

*期間*の可能な値 : 
- 年
- Quarter
- 月
- Week
- 日
- 時
- 分
- 秒
- Millisecond
- マイクロ 秒
- ナノ秒

**戻り値**

減算`periods`の結果の量を表す整数 (`datetime_1` - `datetime_2`)

**使用例**

```kusto
print
year = datetime_diff('year',datetime(2017-01-01),datetime(2000-12-31)),
quarter = datetime_diff('quarter',datetime(2017-07-01),datetime(2017-03-30)),
month = datetime_diff('month',datetime(2017-01-01),datetime(2015-12-30)),
week = datetime_diff('week',datetime(2017-10-29 00:00),datetime(2017-09-30 23:59)),
day = datetime_diff('day',datetime(2017-10-29 00:00),datetime(2017-09-30 23:59)),
hour = datetime_diff('hour',datetime(2017-10-31 01:00),datetime(2017-10-30 23:59)),
minute = datetime_diff('minute',datetime(2017-10-30 23:05:01),datetime(2017-10-30 23:00:59)),
second = datetime_diff('second',datetime(2017-10-30 23:00:10.100),datetime(2017-10-30 23:00:00.900)),
millisecond = datetime_diff('millisecond',datetime(2017-10-30 23:00:00.200100),datetime(2017-10-30 23:00:00.100900)),
microsecond = datetime_diff('microsecond',datetime(2017-10-30 23:00:00.1009001),datetime(2017-10-30 23:00:00.1008009)),
nanosecond = datetime_diff('nanosecond',datetime(2017-10-30 23:00:00.0000000),datetime(2017-10-30 23:00:00.0000007))
```

|year|quarter|month|week|day|hour|minute|second|ミリ秒|マイクロ秒|ナノ秒|
|---|---|---|---|---|---|---|---|---|---|---|
|17|2|13|5|29|2|5|10|100|100|-700|



