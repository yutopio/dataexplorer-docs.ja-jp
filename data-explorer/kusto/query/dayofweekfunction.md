---
title: dayofweek ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの dayofweek () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 04b6122c7517d79d5563892a621eed8cde3b948a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348481"
---
# <a name="dayofweek"></a>dayofweek()

前の日曜日からの日数を表す整数を返し `timespan` ます。

```kusto
dayofweek(datetime(2015-12-14)) == 1d  // Monday
```

## <a name="syntax"></a>構文

`dayofweek(`*a_date*`)`

## <a name="arguments"></a>引数

* `a_date`:`datetime`。

## <a name="returns"></a>戻り値

前の日曜日の午前 0 時からの `timespan`。日数を示す整数に切り捨てられます。

## <a name="examples"></a>使用例

```kusto
dayofweek(datetime(1947-11-30 10:00:05))  // time(0.00:00:00), indicating Sunday
dayofweek(datetime(1970-05-11))           // time(1.00:00:00), indicating Monday
```
