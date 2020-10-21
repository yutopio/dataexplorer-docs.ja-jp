---
title: startofday ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの startofday () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 450863ec6360a40d1fea452dd0427e54f21c6c71
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241099"
---
# <a name="startofday"></a>startofday()

指定されている場合、オフセットによってシフトされた日付を含む日の開始を返します。

## <a name="syntax"></a>構文

`startofday(`*日付*[ `,` *オフセット*]`)`

## <a name="arguments"></a>引数

* `date`: 入力された日付。
* `offset`: 入力された日付からのオフセット日数 (省略可能) (整数、既定値-0)。 

## <a name="returns"></a>戻り値

指定された場合、オフセットを使用して、指定された *日付* 値の日の開始を表す datetime。

## <a name="example"></a>例

```kusto
  range offset from -1 to 1 step 1
 | project dayStart = startofday(datetime(2017-01-01 10:10:17), offset) 
```

|dayStart|
|---|
|2016-12-31 00:00: 00.0000000|
|2017-01-01 00:00: 00.0000000|
|2017-01-02 00:00: 00.0000000|