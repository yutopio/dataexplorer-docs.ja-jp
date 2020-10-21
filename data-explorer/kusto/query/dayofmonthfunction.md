---
title: dayofmonth ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの dayofmonth () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e98e1405a6521ef9cdaf40a24b2c173bdf72c376
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252407"
---
# <a name="dayofmonth"></a>dayofmonth()

指定された月の日を表す整数値を返します

```kusto
dayofmonth(datetime(2015-12-14)) == 14
```

## <a name="syntax"></a>構文

`dayofmonth(`*a_date*`)`

## <a name="arguments"></a>引数

* `a_date`:`datetime`。

## <a name="returns"></a>戻り値

`day number` 指定された月の。