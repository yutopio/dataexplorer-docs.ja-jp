---
title: rand ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの rand () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1206f72b3951601c105de450bc6923861ad011ae
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241280"
---
# <a name="rand"></a>rand()

乱数を返します。

```kusto
rand()
rand(1000)
```

## <a name="syntax"></a>構文

* `rand()` - `real` [0.0, 1.0) の範囲内で一様分布を持つ型の値を返します。
* `rand(`*N* `)` - `real` セット {0.0, 1.0,..., *n-1* } からの一様分布で選択された型の値を返します。