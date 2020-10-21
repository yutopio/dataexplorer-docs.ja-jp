---
title: monthofyear ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの monthofyear () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd40612236b9c9b249c9c070bc784c518be0faae
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250999"
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

`month number` 指定された年の。