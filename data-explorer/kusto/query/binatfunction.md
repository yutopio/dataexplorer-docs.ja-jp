---
title: bin_at ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの bin_at () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 90055f644dbf653eb65546202832f7cab834a0ac
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227598"
---
# <a name="bin_at"></a>bin_at()

ビンの開始点を制御して、値を固定サイズの "ビン" に切り捨てます。
(「」も参照してください [`bin function`](./binfunction.md) )。

**構文**

`bin_at``(`*式*ビン `,` *サイズ* `, ` *fixedpoint*`)`

**引数**

* *式*: 数値型のスカラー式 (およびを含む `datetime` `timespan` )。丸め対象の値を示します。
* *Binsize*: 各ビンのサイズを示す*式*と同じ型のスカラー定数。 
* *Fixedpoint*: "固定ポイント" (つまり、値*Expression* ) である*expression*の1つの値を示す式と同じ型のスカラー定数。 `fixed_point` `bin_at(fixed_point, bin_size, fixed_point) == fixed_point`

**戻り値**

下にあるビン*サイズ*の最も近い倍数をシフトして、 *fixedpoint*がそれ自体に変換されるよう*にします*。

**使用例**

|正規表現                                                                    |結果                           |説明                   |
|------------------------------------------------------------------------------|---------------------------------|---------------------------|
|`bin_at(6.5, 2.5, 7)`                                                         |`4.5`                            ||
|`bin_at(time(1h), 1d, 12h)`                                                   |`-12h`                           ||
|`bin_at(datetime(2017-05-15 10:20:00.0), 1d, datetime(1970-01-01 12:00:00.0))`|`datetime(2017-05-14 12:00:00.0)`|すべてのビンが正午になります   |
|`bin_at(datetime(2017-05-17 10:20:00.0), 7d, datetime(2017-06-04 00:00:00.0))`|`datetime(2017-05-14 00:00:00.0)`|すべてのビンが日曜日に表示されます|


次の例では、 `"fixed point"` arg がビンの1つとして返され、他のビンがに基づいてそのビンにアラインされていることに注意して `bin_size` ください。 また、各 datetime bin は、その bin の開始時刻を表しています。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto

datatable(Date:datetime, Num:int)[
datetime(2018-02-24T15:14),3,
datetime(2018-02-23T16:14),4,
datetime(2018-02-26T15:14),5]
| summarize sum(Num) by bin_at(Date, 1d, datetime(2018-02-24 15:14:00.0000000)) 
```

|Date|sum_Num|
|---|---|
|2018-02-23 15:14: 00.0000000|4|
|2018-02-24 15:14: 00.0000000|3|
|2018-02-26 15:14: 00.0000000|5|
