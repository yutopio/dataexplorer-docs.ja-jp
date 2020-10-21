---
title: limit 演算子-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの制限演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e30bc631af2d055d7cf8c2720a60637f4b76f8cb
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246576"
---
# <a name="limit-operator"></a>limit 演算子

指定された行数まで返します。

```kusto
T | limit 5
```

**Alias**

[take 演算子](takeoperator.md)