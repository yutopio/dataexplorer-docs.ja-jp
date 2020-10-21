---
title: getyear ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの getyear () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a820d05b453be7cc991f8068644d33ff990115fc
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251144"
---
# <a name="getyear"></a>getyear()

引数の年の部分を返し `datetime` ます。

## <a name="example"></a>例

```kusto
T
| extend year = getyear(datetime(2015-10-12))
// year == 2015
```