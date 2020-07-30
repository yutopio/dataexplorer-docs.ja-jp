---
title: endofmonth ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーにおける endofmonth () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 772cf42bfb4bd96a9cff94b7723b234139da462a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348294"
---
# <a name="endofmonth"></a>endofmonth()

指定されている場合、オフセットでシフトした日付を含む月の終わりを返します。

## <a name="syntax"></a>構文

`endofmonth(`*日付*[ `,` *オフセット*]`)`

## <a name="arguments"></a>引数

* `date`: 入力された日付。
* `offset`: 入力された日付からのオフセット月数 (省略可能) (整数、既定値-0)。

## <a name="returns"></a>戻り値

指定された場合、オフセットを使用して、指定された*日付*値の月の終わりを表す datetime。

## <a name="example"></a>例

```kusto
  range offset from -1 to 1 step 1
 | project monthEnd = endofmonth(datetime(2017-01-01 10:10:17), offset) 
```

|月の終了日|
|---|
|2016-12-31 23:59: 59.9999999|
|2017-01-31 23:59: 59.9999999|
|2017-02-28 23:59: 59.9999999|