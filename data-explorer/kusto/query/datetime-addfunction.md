---
title: datetime_add() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの datetime_add() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ead7e0ae5c4dee94930afe1b20c4d5b99e2b4664
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516458"
---
# <a name="datetime_add"></a>datetime_add()

指定した日付部分に指定した金額を掛け合って、指定した[日時](./scalar-data-types/datetime.md)に加算した新しい[日時](./scalar-data-types/datetime.md)を計算します。

**構文**

`datetime_add(`*期間*`,`*amount*金額`,`*日時*`)`

**引数**

* `period`:[文字列](./scalar-data-types/string.md)。 
* `amount`:[整数](./scalar-data-types/int.md)。
* `datetime`:[日時](./scalar-data-types/datetime.md)値。

*期間*の可能な値 : 
- 年
- Quarter
- 月
- Week
- 日
- 時
- 分
- 秒
- Millisecond
- マイクロ 秒
- ナノ秒

**戻り値**

特定の日時間隔の後の日付が追加されました。

**使用例**

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
|2018-01-01 00:00:00.0000000|2017-04-01 00:00:00.0000000|2017-02-01 00:00:00.0000000|2017-01-08 00:00:00.0000000|2017-01-02 00:00:00.0000000|2017-01-01 01:00:00.0000000|2017-01-01 00:01:00.0000000|2017-01-01 00:00:01.0000000|






