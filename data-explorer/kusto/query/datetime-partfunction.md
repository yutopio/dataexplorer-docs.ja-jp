---
title: datetime_part ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの datetime_part () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: c786f0edc94a9b92ca0f4484d0d71166ee699883
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803983"
---
# <a name="datetime_part"></a>datetime_part()

要求された日付部分を整数値として抽出します。

```kusto
datetime_part("Day",datetime(2015-12-14))
```

## <a name="syntax"></a>構文

`datetime_part(`*パート* `,`*datetime*`)`

## <a name="arguments"></a>引数

* `date`: `datetime`
* `part`: `string`

使用可能な値 `part` : 
* Year
* Quarter
* Month
* week_of_year
* 日間
* DayOfYear
* Hour
* 分
* Second
* Millisecond
* Microsecond
* Nanosecond

## <a name="returns"></a>戻り値

抽出された部分を表す整数。

> [!NOTE]
> `week_of_year`週番号を表す整数を返します。 週番号は、1年の最初の週から計算されます。これは、最初の木曜日を含んでいます。

## <a name="examples"></a>例

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
> `weekofyear`は、一部の古いバリアントです `week_of_year` 。 `weekofyear`が ISO 8601 に準拠していませんでした。1年の最初の週は、年の最初の水曜日を含む週として定義されています。
> `week_of_year`は ISO 8601 に準拠しています。年の最初の週は、その年の最初の木曜日の週として定義されます。 [詳細情報](https://en.wikipedia.org/wiki/ISO_8601#Week_dates)。