---
title: datetime_part() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでdatetime_part() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: c64208725f0d5c49a7ea7733f8eb5a208e19225b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516373"
---
# <a name="datetime_part"></a>datetime_part()

要求された日付部分を整数値として抽出します。

```kusto
datetime_part("Day",datetime(2015-12-14))
```

**構文**

`datetime_part(`*パートタイム*`,`*の日時*`)`

**引数**

* `date`: `datetime`
* `part`: `string`

の値を`part`指定できます。 
- 年
- Quarter
- 月
- week_of_year
- 日
- DayOfYear
- 時
- 分
- 秒
- Millisecond
- マイクロ 秒
- ナノ秒

**戻り値**

抽出されたパーツを表す整数。

**注**

`week_of_year`は週番号を表す整数を返します。 週番号は、最初の木曜日を含む 1 年の最初の週から計算されます。

**使用例**

```kusto
let dt = datetime(2017-10-30 01:02:03.7654321); 
print 
year = datetime_part("year", dt),
quarter = datetime_part("quarter", dt),
month = datetime_part("month", dt),
weekOfYear = datetime_part("week_of_year", dt),
day = datetime_part("day", dt),
dayOfYear = datetime_part("dayOfYear", dt),
hour = datetime_part("hour", dt),
minute = datetime_part("minute", dt),
second = datetime_part("second", dt),
millisecond = datetime_part("millisecond", dt),
microsecond = datetime_part("microsecond", dt),
nanosecond = datetime_part("nanosecond", dt)

```

|year|quarter|month|weekOfYear|day|dayOfYear|hour|minute|second|ミリ秒|マイクロ秒|ナノ秒|
|---|---|---|---|---|---|---|---|---|---|---|---|
|2017|4|10|44|30|303|1|2|3|765|765432|765432100|

> [!NOTE]
> `weekofyear`は、古い部分`week_of_year`の変種です。 `weekofyear`ISO 8601 準拠ではありません。年の最初の週は、その年の最初の水曜日を含む週として定義されました。
`week_of_year`は ISO 8601 準拠です。年の最初の週は、その年の最初の木曜日を含む週として定義されます。 [詳細については](https://en.wikipedia.org/wiki/ISO_8601#Week_dates)、を参照してください。