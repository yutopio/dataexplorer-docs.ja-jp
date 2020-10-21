---
title: unixtime_milliseconds_todatetime ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの unixtime_milliseconds_todatetime () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: 9f029b18a147907a9e77e5ade7a1b708d3bed877
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245663"
---
# <a name="unixtime_milliseconds_todatetime"></a>unixtime_milliseconds_todatetime()

Unix-エポックミリ秒を UTC の datetime に変換します。

## <a name="syntax"></a>構文

`unixtime_milliseconds_todatetime(*milliseconds*)`

## <a name="arguments"></a>引数

* *ミリ秒*: 実数はエポックタイムスタンプをミリ秒単位で表します。 `Datetime` エポック時間 (1970-01-01 00:00:00) が負のタイムスタンプ値を持つ前に発生します。

## <a name="returns"></a>戻り値

変換が成功した場合、結果は [datetime](./scalar-data-types/datetime.md) 値になります。 変換に失敗した場合、結果は null になります。

## <a name="see-also"></a>関連項目

* [Unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md)を使用して unix-エポック秒を UTC 日時に変換します。
* [Unixtime_microseconds_todatetime ()](unixtime-microseconds-todatetimefunction.md)を使用して、unix-エポックマイクロ秒を UTC 日時に変換します。
* [Unixtime_nanoseconds_todatetime ()](unixtime-nanoseconds-todatetimefunction.md)を使用して、unix-エポックナノ秒を UTC の datetime に変換します。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_milliseconds_todatetime(1546300800000)
```

|date_time|
|---|
|2019-01-01 00:00: 00.0000000|
