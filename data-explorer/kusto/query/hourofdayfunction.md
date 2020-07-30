---
title: hourofday ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの hourofday () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a001a2f9faa1eb7ea3636ee6fbfde3cb0489158
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347512"
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

`hour number`(0-23)。