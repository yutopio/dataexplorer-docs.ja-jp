---
title: Datetime/timespan 算術-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの Datetime/timespan 演算について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 0b70cb1730d893e07790ade3079be21da209648c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247674"
---
# <a name="datetime--timespan-arithmetic"></a>Datetime/timespan 算術

Kusto は、型と型の値に対して算術演算を実行することをサポート `datetime` してい `timespan` ます。

* 2つの値を減算 `datetime` して値を取得し、 `timespan` その差を表すことができます。
  たとえば、 `datetime(1997-06-25) - datetime(1910-06-11)` は、彼が Cousteau したときの古い [jacques victor 氏](https://en.wikipedia.org/wiki/Jacques_Cousteau) を示しています。

* 2つの値を加算または減算して、値の合計または差を求めることができ `timespan` `timespan` ます。
  たとえば、 `1d + 2d` は3日です。

* 値の値を加算または減算でき `timespan` `datetime` ます。
  たとえば、 `datetime(1910-06-11) + 1d` は日付 Cousteau が1日前に変更されたことを示します。

* 2つ `timespan` の値を除算して商を取得できます。
  たとえば、はを `1d / 5h` 示し `4.8` ます。
  これにより、任意の値を `timespan` 別の値の倍数として表すことができ `timespan` ます。 たとえば、時間を秒単位で表すには、 `1h` `1s` `1h / 1s` (明確な結果を含む) を使用して単純に除算 `3600` します。

* 逆に、値を `double` `long` `timespan` 取得するために値を使用して、などの数値を複数指定することもでき `timespan` ます。
  たとえば、1時間を表すことができ `1.5 * 1h` ます。

## <a name="example-unix-time"></a>例: Unix 時刻

[Unix 時間](https://en.wikipedia.org/wiki/Unix_time) (POSIX 時間または Unix エポック時間とも呼ばれます) は、00:00:00 木曜日、1月1970、世界協定時刻 (UTC) から閏年秒を引いた経過時間を秒単位で示すためのシステムです。

データに Unix 時間の表現が整数として含まれている場合、またはデータを変換する必要がある場合は、次の関数を使用できます。

```kusto
let fromUnixTime = (t:long)
{ 
    datetime(1970-01-01) + t * 1sec 
};
print result = fromUnixTime(1546897531)
```

|結果                     |
|---------------------------|
|2019-01-07 21:45: 31.0000000|

```kusto
let toUnixTime = (dt:datetime) 
{ 
    (dt - datetime(1970-01-01)) / 1s 
};
print result = toUnixTime(datetime(2019-01-07 21:45:31.0000000))
```

|結果                     |
|---------------------------|
|1546897531                 |

> [!NOTE]
> 上記の関数に加えて、「unix エポック時間変換用の専用関数: [unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md) 
>  [unixtime_milliseconds_todatetime (](unixtime-milliseconds-todatetimefunction.md)) 
>  [unixtime_microseconds_todatetime (](unixtime-microseconds-todatetimefunction.md)) 
>  [unixtime_nanoseconds_todatetime ()](unixtime-nanoseconds-todatetimefunction.md) 」を参照してください。