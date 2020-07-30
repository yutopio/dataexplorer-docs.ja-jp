---
title: datetime_add ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの datetime_add () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 766f0617b70e21194d731ae1cf8eabf1014265bb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348549"
---
# <a name="datetime_add"></a>datetime_add()

指定された[datetime](./scalar-data-types/datetime.md)に加算された、指定した datepart から指定された量を乗算した新しい[datetime](./scalar-data-types/datetime.md)を計算します。

## <a name="syntax"></a>構文

`datetime_add(`*期間* `,`*金額* `,`*datetime*`)`

## <a name="arguments"></a>引数

* `period`:[文字列](./scalar-data-types/string.md)。 
* `amount`:[整数](./scalar-data-types/int.md)。
* `datetime`: [datetime](./scalar-data-types/datetime.md)値。

有効*期間*の値: 
- Year
- Quarter
- Month
- Week
- 日
- Hour
- 分
- Second
- Millisecond
- Microsecond
- Nanosecond

## <a name="returns"></a>戻り値

特定の時刻/日付間隔が追加された日付。

## <a name="examples"></a>例

```kusto
print  year = datetime_add('year',1,make_datetime(2017,1,1)),
quarter = datetime_add('quarter',1,make_datetime(2017,1,1)),
month = datetime_add('month',1,make_datetime(2017,1,1)),
week = datetime_add('week',1,make_datetime(2017,1,1)),
day = datetime_add('day',1,make_datetime(2017,1,1)),
hour = datetime_add('hour',1,make_datetime(2017,1,1)),
minute = datetime_add('minute',1,make_datetime(2017,1,1)),
second = datetime_add('second',1,make_datetime(2017,1,1))

```

|year|quarter|month|week|day|hour|minute|second|
|---|---|---|---|---|---|---|---|
|2018-01-01 00:00: 00.0000000|2017-04-01 00:00: 00.0000000|2017-02-01 00:00: 00.0000000|2017-01-08 00:00: 00.0000000|2017-01-02 00:00: 00.0000000|2017-01-01 01:00: 00.0000000|2017-01-01 00:01: 00.0000000|2017-01-01 00:00: 01.0000000|






