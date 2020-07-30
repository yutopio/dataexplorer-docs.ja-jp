---
title: monthofyear ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの monthofyear () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: af015146bb2f07d83d4333312a96d5b80c67190a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346730"
---
# <a name="monthofyear"></a>monthofyear()

指定された年の月番号を表す整数を返します。

別のエイリアス: getmonth ()

```kusto
monthofyear(datetime("2015-12-14"))
```

## <a name="syntax"></a>構文

`monthofyear(`*a_date*`)`

## <a name="arguments"></a>引数

* `a_date`:`datetime`。

## <a name="returns"></a>戻り値

`month number`指定された年の。