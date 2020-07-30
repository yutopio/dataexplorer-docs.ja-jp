---
title: rand ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの rand () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 53fc7512c1a0fb2019526f48ade54fabbd351d05
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345931"
---
# <a name="rand"></a>rand()

乱数を返します。

```kusto
rand()
rand(1000)
```

## <a name="syntax"></a>構文

* `rand()`- `real` [0.0, 1.0) の範囲内で一様分布を持つ型の値を返します。
* `rand(`*N* `)` - `real` セット {0.0, 1.0,..., *n-1* } からの一様分布で選択された型の値を返します。