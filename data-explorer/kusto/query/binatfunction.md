---
title: bin_at() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの bin_at() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 218142e1c377e6a72abde154d4576025698b2f0d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517444"
---
# <a name="bin_at"></a>bin_at()

値を固定サイズの "bin" に切り捨て、ビンの開始点を制御します。
(も参照[`bin function`](./binfunction.md)してください。

**構文**

`bin_at``(`*Expression*式`,`*ビン*サイズ`, `*固定点*`)`

**引数**

* *式*: 数値型のスカラー式 ( `datetime` `timespan`および ) は、丸める値を示します。
* *BinSize*: 各ビンのサイズを示す*式*と同じ型のスカラー定数。 
* *FixedPoint*:*式*と同じ型のスカラー定数で、*式*の値が 「固定点」であることを示す (つまり`fixed_point`、.) `bin_at(fixed_point, bin_size, fixed_point) == fixed_point`

**戻り値**

式*の下の* *BinSize*の最も近い倍数がシフトされ *、FixedPoint*がそれ自体に変換されます。

**使用例**

|式                                                                    |結果                           |説明                   |
|------------------------------------------------------------------------------|---------------------------------|---------------------------|
|`bin_at(6.5, 2.5, 7)`                                                         |`4.5`                            ||
|`bin_at(time(1h), 1d, 12h)`                                                   |`-12h`                           ||
|`bin_at(datetime(2017-05-15 10:20:00.0), 1d, datetime(1970-01-01 12:00:00.0))`|`datetime(2017-05-14 12:00:00.0)`|すべてのビンは正午になります   |
|`bin_at(datetime(2017-05-17 10:20:00.0), 7d, datetime(2017-06-04 00:00:00.0))`|`datetime(2017-05-14 00:00:00.0)`|すべてのビンは日曜日になります|


次の例では`"fixed point"`、arg が 1 つのビンとして返され、他のビンが`bin_size`に基づいてそのビンに揃えられていることに注目してください。 また、各 datetime bin は、そのビンの開始時刻を表していることにも注意してください。

```kusto

datatable(Date:datetime, Num:int)[
datetime(2018-02-24T15:14),3,
datetime(2018-02-23T16:14),4,
datetime(2018-02-26T15:14),5]
| summarize sum(Num) by bin_at(Date, 1d, datetime(2018-02-24 15:14:00.0000000)) 
```

|Date|sum_Num|
|---|---|
|2018-02-23 15:14:00.0000000|4|
|2018-02-24 15:14:00.0000000|3|
|2018-02-26 15:14:00.0000000|5|