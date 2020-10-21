---
title: dayofyear ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの dayofyear () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2ac301365cb22849dc07ea137756f4093bea79b2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252391"
---
# <a name="dayofyear"></a>dayofyear()

指定された年の日番号を表す整数を返します。

```kusto
dayofyear(datetime(2015-12-14))
```

## <a name="syntax"></a>構文

`dayofweek(`*a_date*`)`

## <a name="arguments"></a>引数

* `a_date`:`datetime`。

## <a name="returns"></a>戻り値

`day number` 指定された年の。