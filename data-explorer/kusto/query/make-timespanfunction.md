---
title: make_timespan ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの make_timespan () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3904f852fdf813d8b2aff264d6b1bc0019335d78
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346951"
---
# <a name="make_timespan"></a>make_timespan()

指定された期間から[timespan](./scalar-data-types/timespan.md)スカラー値を作成します。

```kusto
make_timespan(1,12,30,55.123) == time(1.12:30:55.123)
```

## <a name="syntax"></a>構文

`make_timespan(`*hour*、*minute*`)`

`make_timespan(`*hour*、*minute*、*second*`)`

`make_timespan(`*day*、*hour*、*minute*、*second*`)`

## <a name="arguments"></a>引数

* *day*: day (正の整数)
* *hour*: hour (0 ~ 23 の整数値)
* *minute*: minute (0 ~ 59 の整数値)
* *second*: second (0 から59.9999999 の実際の値)

## <a name="returns"></a>戻り値

作成が成功した場合、結果は[timespan](./scalar-data-types/timespan.md)値になります。それ以外の場合、結果は null になります。
 
## <a name="example"></a>例

```kusto
print ['timespan'] = make_timespan(1,12,30,55.123)

```

|TimeSpan|
|---|
|1.12:30: 55.1230000|


