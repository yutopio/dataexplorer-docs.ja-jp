---
title: startofday ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの startofday () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3b44da313c50360c73f63f1244ce2be959af2eb4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350946"
---
# <a name="startofday"></a>startofday()

指定されている場合、オフセットによってシフトされた日付を含む日の開始を返します。

## <a name="syntax"></a>構文

`startofday(`*日付*[ `,` *オフセット*]`)`

## <a name="arguments"></a>引数

* `date`: 入力された日付。
* `offset`: 入力された日付からのオフセット日数 (省略可能) (整数、既定値-0)。 

## <a name="returns"></a>戻り値

指定された場合、オフセットを使用して、指定された*日付*値の日の開始を表す datetime。

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