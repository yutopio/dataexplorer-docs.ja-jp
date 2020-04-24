---
title: make_datetime() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの make_datetime() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c51b99e4d668ec4a5f96cdfd72442d7bcab553a5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512922"
---
# <a name="make_datetime"></a>make_datetime()

指定した[日時から日付時刻](./scalar-data-types/datetime.md)スカラー値を作成します。

```kusto
make_datetime(2017,10,01,12,10) == datetime(2017-10-01 12:10)
```

**構文**

`make_datetime(`*年*,*月*,*日*`)`

`make_datetime(`*年*,*月*,*日*,*時*,*分*`)`

`make_datetime(`*年*,*月*,*日*,*時間*,*分*,*秒*`)`

**引数**

* *年*: 年 (0 から 9999 までの整数値)
* *月*: 月 (1 から 12 までの整数値)
* *day*: 日 (1 から 28-31 までの整数値)
* *時*: 時 (0 から 23 までの整数値)
* *分*: 分 (0 から 59 までの整数値)
* *2 番目*の場合 (0 から 59.9999999 までの実数値)

**戻り値**

作成が成功すると、結果は[日時](./scalar-data-types/datetime.md)値になり、それ以外の場合は結果は NULL になります。
 
**例**

```kusto
print year_month_day = make_datetime(2017,10,01)
```

|year_month_day|
|---|
|2017-10-01 00:00:00.0000000|




```kusto
print year_month_day_hour_minute = make_datetime(2017,10,01,12,10)
```

|year_month_day_hour_minute|
|---|
|2017-10-01 12:10:00.0000000|




```kusto
print year_month_day_hour_minute_second = make_datetime(2017,10,01,12,11,0.1234567)
```

|year_month_day_hour_minute_second|
|---|
|2017-10-01 12:11:00.1234567|

