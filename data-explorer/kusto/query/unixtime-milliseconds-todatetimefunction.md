---
title: unixtime_milliseconds_todatetime() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで unixtime_milliseconds_todatetime() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: ab229d78d2a9ff5a7e50ecefe027824488578b12
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505255"
---
# <a name="unixtime_milliseconds_todatetime"></a>unixtime_milliseconds_todatetime()

UNIX エポックミリ秒を UTC 日時に変換します。

**構文**

`unixtime_milliseconds_todatetime(*milliseconds*)`

**引数**

* *ミリ秒*: 実数はエポック タイムスタンプをミリ秒単位で表します。 `Datetime`エポック時間 (1970-01-01 00:00:00) の前に発生するタイムスタンプ値が負の値です。

**戻り値**

変換が成功すると、結果は[日時](./scalar-data-types/datetime.md)値になります。 変換が成功しなかった場合、結果は null になります。

**参照**

* [unixtime_seconds_todatetime()](unixtime-seconds-todatetimefunction.md)を使用して unix-エポック秒を UTC 日時に変換します。
* [unixtime_microseconds_todatetime()](unixtime-microseconds-todatetimefunction.md)を使用して、unix-エポックマイクロ秒を UTC 日時に変換します。
* [unixtime_nanoseconds_todatetime()](unixtime-nanoseconds-todatetimefunction.md)を使用して、unix-エポックナノ秒を UTC 日時に変換します。

**例**

```kusto
print date_time = unixtime_milliseconds_todatetime(1546300800000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|