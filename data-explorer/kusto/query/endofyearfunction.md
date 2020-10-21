---
title: endofyear ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーにおける endofyear () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a93b24af949beedc02ddd3dbbc479074f0aa891c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249134"
---
# <a name="endofyear"></a>endofyear()

指定されている場合、オフセットでシフトした日付を含む年の最後の日付を返します。

## <a name="syntax"></a>構文

`endofyear(`*日付*[ `,` *オフセット*]`)`

## <a name="arguments"></a>引数

* `date`: 入力された日付。
* `offset`: 入力された日付からのオフセット年数 (省略可能) (整数、既定値-0)。

## <a name="returns"></a>戻り値

指定された場合、オフセットを使用して、指定された *日付* 値の年の終わりを表す datetime。

## <a name="example"></a>例

```kusto
  range offset from -1 to 1 step 1
 | project yearEnd = endofyear(datetime(2017-01-01 10:10:17), offset) 
```

|yearEnd|
|---|
|2016-12-31 23:59: 59.9999999|
|2017-12-31 23:59: 59.9999999|
|2018-12-31 23:59: 59.9999999|