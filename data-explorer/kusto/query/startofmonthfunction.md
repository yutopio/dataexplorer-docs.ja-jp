---
title: startofmonth ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの startofmonth () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c7e13cf809174fd258f9e7245bb2e537e2408c28
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241740"
---
# <a name="startofmonth"></a>startofmonth()

指定されている場合、オフセットによってシフトされた日付を含む月の開始を返します。

## <a name="syntax"></a>構文

`startofmonth(`*日付*[ `,` *オフセット*]`)`

## <a name="arguments"></a>引数

* `date`: 入力された日付。
* `offset`: 入力された日付からのオフセット月数 (省略可能) (整数、既定値-0)。

## <a name="returns"></a>戻り値

指定された場合、オフセットを使用して、指定された *日付* 値の月の開始を表す datetime。

## <a name="example"></a>例

```kusto
  range offset from -1 to 1 step 1
 | project monthStart = startofmonth(datetime(2017-01-01 10:10:17), offset) 
```

|月の開始日|
|---|
|2016-12-01 00:00: 00.0000000|
|2017-01-01 00:00: 00.0000000|
|2017-02-01 00:00: 00.0000000|