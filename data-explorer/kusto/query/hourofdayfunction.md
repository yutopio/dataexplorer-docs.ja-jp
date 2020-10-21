---
title: hourofday ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの hourofday () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6d83725e067f12d05382a905aee935f9433ef7a9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244731"
---
# <a name="hourofday"></a>hourofday()

指定された日付の時間番号を表す整数値を返します。

```kusto
hourofday(datetime(2015-12-14 18:54)) == 18
```

## <a name="syntax"></a>構文

`hourofday(`*a_date*`)`

## <a name="arguments"></a>引数

* `a_date`:`datetime`。

## <a name="returns"></a>戻り値

`hour number` (0-23)。