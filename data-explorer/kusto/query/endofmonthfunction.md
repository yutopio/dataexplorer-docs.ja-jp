---
title: endofmonth ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーにおける endofmonth () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1911546f95b86a04fe59d22137b1eaeff22e2c2a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245147"
---
# <a name="endofmonth"></a>endofmonth()

指定されている場合、オフセットでシフトした日付を含む月の終わりを返します。

## <a name="syntax"></a>構文

`endofmonth(`*日付*[ `,` *オフセット*]`)`

## <a name="arguments"></a>引数

* `date`: 入力された日付。
* `offset`: 入力された日付からのオフセット月数 (省略可能) (整数、既定値-0)。

## <a name="returns"></a>戻り値

指定された場合、オフセットを使用して、指定された *日付* 値の月の終わりを表す datetime。

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