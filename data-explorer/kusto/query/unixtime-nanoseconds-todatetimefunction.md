---
title: unixtime_nanoseconds_todatetime ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの unixtime_nanoseconds_todatetime () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: 0786863589ece863278f9cadec79c9c86a4117eb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350606"
---
# <a name="unixtime_nanoseconds_todatetime"></a>unixtime_nanoseconds_todatetime()

Unix-エポックナノ秒を UTC の datetime に変換します。

## <a name="syntax"></a>構文

`unixtime_nanoseconds_todatetime(*nanoseconds*)`

## <a name="arguments"></a>引数

* *ナノ秒*: 実数は、エポックタイムスタンプをナノ秒単位で表します。 `Datetime`エポック時間 (1970-01-01 00:00:00) が負のタイムスタンプ値を持つ前に発生します。

## <a name="returns"></a>戻り値

変換が成功した場合、結果は[datetime](./scalar-data-types/datetime.md)値になります。 変換に失敗した場合、結果は null になります。

**参照**

* [Unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md)を使用して unix-エポック秒を UTC 日時に変換します。
* [Unixtime_milliseconds_todatetime ()](unixtime-milliseconds-todatetimefunction.md)を使用して unix-エポックミリ秒を UTC 日時に変換します。
* [Unixtime_microseconds_todatetime ()](unixtime-microseconds-todatetimefunction.md)を使用して、unix-エポックマイクロ秒を UTC 日時に変換します。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_nanoseconds_todatetime(1546300800000000000)
```

|date_time|
|---|
|2019-01-01 00:00: 00.0000000|
