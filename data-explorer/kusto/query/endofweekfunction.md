---
title: endofweek ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーにおける endofweek () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 57fa1764753e730f9ff0a2b01a70e0c221217d23
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348277"
---
# <a name="endofweek"></a>endofweek()

指定されている場合、オフセットでシフトされた日付を含む週の終わりを返します。

週の最終日は土曜日と見なされます。

## <a name="syntax"></a>構文

`endofweek(`*日付*[ `,` *オフセット*]`)`

## <a name="arguments"></a>引数

* `date`: 入力された日付。
* `offset`: 入力された日付 (integer、既定値は 0) からのオフセット週の数 (省略可能)。

## <a name="returns"></a>戻り値

指定された場合、オフセットを使用して、指定された*日付*値の週の終わりを表す datetime。

## <a name="example"></a>例

```kusto
  range offset from -1 to 1 step 1
 | project weekEnd = endofweek(datetime(2017-01-01 10:10:17), offset)  

```

|除く|
|---|
|2016-12-31 23:59: 59.9999999|
|2017-01-07 23:59: 59.9999999|
|2017-01-14 23:59: 59.9999999|