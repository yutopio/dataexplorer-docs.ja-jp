---
title: 日時/時間間隔算術 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの日時/タイムスパン算術計算について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/27/2019
ms.openlocfilehash: 34bda7b8418dd519995f25c625a5031202a64a8b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516322"
---
# <a name="datetime--timespan-arithmetic"></a>日時/時間間隔算術演算

Kusto は型`datetime`の値に対する算術`timespan`演算の実行をサポートしています。

* 2`datetime`つの値`timespan`を減算 (加算しない) ことができます。
  例えば、`datetime(1997-06-25) - datetime(1910-06-11)`[ジャック・イヴ・クストー](https://en.wikipedia.org/wiki/Jacques_Cousteau)が死んだとき、何歳だったかです。

* 1 つは、2`timespan`つの値を加算`timespan`または減算して、その合計または差である値を取得できます。
  たとえば、3`1d + 2d`日間です。

* 値を加算または値から`timespan``datetime`減算できます。
  例えば、`datetime(1910-06-11) + 1d`クストーが1日前になった日付です。

* 1 つは`timespan`2 つの値を分割して商を取得できます。
  たとえば、`1d / 5h`を`4.8`指定します。
  これにより、任意`timespan`の値を別`timespan`の値の倍数として表現できます。 たとえば、1 時間を秒単位で表現するには、`1h``1s`単に`1h / 1s`: で除算`3600`します (明らかな結果を得ます)。

* 逆に、値を取得するために、数値`double`(および`long`など)`timespan`を数値で複数`timespan`にすることもできます。
  たとえば、1 時間半を`1.5 * 1h`として表現できます。

## <a name="example-unix-time"></a>例: Unix時間

[UNIX 時間](https://en.wikipedia.org/wiki/Unix_time)(POSIX 時間または UNIX エポック時間とも呼ばれる) は、1970 年 1 月 1 日(木曜日)00:00:00 から経過した秒数 (UTC)、うるう秒を引いた値を示すシステムです。

データに Unix 時間の表記が整数として含まれている場合、またはデータに変換する必要がある場合は、次の関数を使用できます。

```kusto
let fromUnixTime = (t:long)
{ 
    datetime(1970-01-01) + t * 1sec 
};
print result = fromUnixTime(1546897531)
```

|結果                     |
|---------------------------|
|2019-01-07 21:45:31.0000000|

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
> 上記の関数に加えて、unix-epoch時間変換の専用関数を参照してください: [unixtime_seconds_todatetime() unixtime_milliseconds_todatetime()](unixtime-seconds-todatetimefunction.md)
> [unixtime_microseconds_todatetime()](unixtime-milliseconds-todatetimefunction.md)
> [unixtime_nanoseconds_todatetime()](unixtime-microseconds-todatetimefunction.md)
> [unixtime_nanoseconds_todatetime()](unixtime-nanoseconds-todatetimefunction.md)