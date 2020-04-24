---
title: rand() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで rand() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d3638d4b979b7318f58efec0bed0da4c31896a9d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510644"
---
# <a name="rand"></a>rand()

乱数を返します。

```kusto
rand()
rand(1000)
```

**構文**

* `rand()`- 範囲 [0.0, 1.0] の範囲で、分布が一様な型`real`の値を返します。
* `rand(`*N* `)` - セット {0.0、 1.0、., `real` *N* - 1} から一様分布で選択された型の値を返します。