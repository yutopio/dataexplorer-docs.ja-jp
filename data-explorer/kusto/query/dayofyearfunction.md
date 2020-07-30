---
title: dayofyear ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの dayofyear () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 925b65846c6ba32163bd325fd2ee3321bc7fc802
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348464"
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

`day number`指定された年の。