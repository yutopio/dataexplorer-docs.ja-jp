---
title: startofweek ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの startofweek () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d5f53dfca4183c4dae623b41abf4c5bce6a3e828
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241193"
---
# <a name="startofweek"></a>startofweek()

指定されている場合、オフセットでシフトされた日付を含む週の開始を返します。

週の開始日は日曜日と見なされます。

## <a name="syntax"></a>構文

`startofweek(`*日付*[ `,` *オフセット*]`)`

## <a name="arguments"></a>引数

* `date`: 入力された日付。
* `offset`: 入力された日付 (integer、既定値は 0) からのオフセット週の数 (省略可能)。

## <a name="returns"></a>戻り値

指定された場合、オフセットを使用して、指定された *日付* 値の週の開始を表す datetime。

## <a name="example"></a>例

```kusto
  range offset from -1 to 1 step 1
 | project weekStart = startofweek(datetime(2017-01-01 10:10:17), offset) 
```

|weekStart|
|---|
|2016-12-25 00:00: 00.0000000|
|2017-01-01 00:00: 00.0000000|
|2017-01-08 00:00: 00.0000000|