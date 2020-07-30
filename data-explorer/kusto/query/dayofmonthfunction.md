---
title: dayofmonth ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの dayofmonth () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3341f416642b06d899c2a3d1f6675f4d3254291f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348498"
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

`day number`指定された月の。