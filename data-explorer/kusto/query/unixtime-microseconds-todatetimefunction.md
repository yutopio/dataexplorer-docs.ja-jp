---
title: unixtime_microseconds_todatetime ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの unixtime_microseconds_todatetime () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: f69261e7ac84d8ae77fd1d2fb89f31ede2536443
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370242"
---
# <a name="unixtime_microseconds_todatetime"></a>unixtime_microseconds_todatetime()

Unix-エポックマイクロ秒を UTC の datetime に変換します。

**構文**

`unixtime_microseconds_todatetime(*microseconds*)`

**引数**

* *マイクロ秒*: 実数は、エポックタイムスタンプをマイクロ秒単位で表します。 `Datetime`エポック時間 (1970-01-01 00:00:00) が負のタイムスタンプ値を持つ前に発生します。

**戻り値**

変換が成功した場合、結果は[datetime](./scalar-data-types/datetime.md)値になります。 変換に失敗した場合、結果は null になります。

**参照**

* [Unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md)を使用して unix-エポック秒を UTC 日時に変換します。
* [Unixtime_milliseconds_todatetime ()](unixtime-milliseconds-todatetimefunction.md)を使用して unix-エポックミリ秒を UTC 日時に変換します。
* [Unixtime_nanoseconds_todatetime ()](unixtime-nanoseconds-todatetimefunction.md)を使用して、unix-エポックナノ秒を UTC の datetime に変換します。

**例**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_microseconds_todatetime(1546300800000000)
```

|date_time|
|---|
|2019-01-01 00:00: 00.0000000|
