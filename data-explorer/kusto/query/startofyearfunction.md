---
title: startofyear ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの startofyear () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 973ea6d5043db0f173fa0ebe98548b20968371b3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241162"
---
# <a name="startofyear"></a>startofyear()

指定されている場合、オフセットによってシフトされた日付を含む年の開始を返します。

## <a name="syntax"></a>構文

`startofyear(`*日付*[ `,` *オフセット*]`)`

## <a name="arguments"></a>引数

* `date`: 入力された日付。
* `offset`: 入力された日付からのオフセット年数 (省略可能) (整数、既定値-0)。 

## <a name="returns"></a>戻り値

指定された場合、オフセットを使用して、指定された *日付* 値の年の開始を表す datetime。

## <a name="example"></a>例

```kusto
  range offset from -1 to 1 step 1
 | project yearStart = startofyear(datetime(2017-01-01 10:10:17), offset) 
```

|yearStart|
|---|
|2016-01-01 00:00: 00.0000000|
|2017-01-01 00:00: 00.0000000|
|2018-01-01 00:00: 00.0000000|