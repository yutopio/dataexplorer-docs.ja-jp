---
title: make_datetime ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの make_datetime () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 589be4fbbc285309e86647ce8d7392840aedea0e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347002"
---
# <a name="make_datetime"></a>make_datetime()

指定された日付と時刻から[datetime](./scalar-data-types/datetime.md)スカラー値を作成します。

```kusto
make_datetime(2017,10,01,12,10) == datetime(2017-10-01 12:10)
```

## <a name="syntax"></a>構文

`make_datetime(`*年*、*月*、*日*`)`

`make_datetime(`*年*、*月*、*日*、*時*、*分*`)`

`make_datetime(`*year*、*month*、*day*、*hour*、*minute*、*second*`)`

## <a name="arguments"></a>引数

* *year*: year (0 ~ 9999 の整数値)
* *month*: month (1 ~ 12 の整数値)
* *day*: day (1 ~ 28-31 の整数値)
* *hour*: hour (0 ~ 23 の整数値)
* *minute*: minute (0 ~ 59 の整数値)
* *second*: second (0 から59.9999999 の実際の値)

## <a name="returns"></a>戻り値

作成が成功した場合、結果は[datetime](./scalar-data-types/datetime.md)値になります。それ以外の場合、結果は null になります。
 
## <a name="example"></a>例

```kusto
print year_month_day = make_datetime(2017,10,01)
```

|year_month_day|
|---|
|2017-10-01 00:00: 00.0000000|




```kusto
print year_month_day_hour_minute = make_datetime(2017,10,01,12,10)
```

|year_month_day_hour_minute|
|---|
|2017-10-01 12:10: 00.0000000|




```kusto
print year_month_day_hour_minute_second = make_datetime(2017,10,01,12,11,0.1234567)
```

|year_month_day_hour_minute_second|
|---|
|2017-10-01 12:11: 00.1234567|

