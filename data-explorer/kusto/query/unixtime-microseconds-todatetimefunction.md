---
title: unixtime_microseconds_todatetime ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの unixtime_microseconds_todatetime () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 713cf936878d0fa311d4637f61881f0481229617
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245708"
---
# <a name="unixtime_microseconds_todatetime"></a>unixtime_microseconds_todatetime()

Unix-エポックマイクロ秒を UTC の datetime に変換します。

## <a name="syntax"></a>構文

`unixtime_microseconds_todatetime(*microseconds*)`

## <a name="arguments"></a>引数

* *マイクロ秒*: 実数は、エポックタイムスタンプをマイクロ秒単位で表します。 `Datetime` エポック時間 (1970-01-01 00:00:00) が負のタイムスタンプ値を持つ前に発生します。

## <a name="returns"></a>戻り値

変換が成功した場合、結果は [datetime](./scalar-data-types/datetime.md) 値になります。 変換に失敗した場合、結果は null になります。

## <a name="see-also"></a>関連項目

* [Unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md)を使用して unix-エポック秒を UTC 日時に変換します。
* [Unixtime_milliseconds_todatetime ()](unixtime-milliseconds-todatetimefunction.md)を使用して unix-エポックミリ秒を UTC 日時に変換します。
* [Unixtime_nanoseconds_todatetime ()](unixtime-nanoseconds-todatetimefunction.md)を使用して、unix-エポックナノ秒を UTC の datetime に変換します。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_microseconds_todatetime(1546300800000000)
```

|date_time|
|---|
|2019-01-01 00:00: 00.0000000|
