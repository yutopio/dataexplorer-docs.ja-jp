---
title: todatetime ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの todatetime () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f5c108b670534728f34db8975f16d713848dd8f4
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264610"
---
# <a name="todatetime"></a>todatetime()

入力を[datetime](./scalar-data-types/datetime.md)スカラーに変換します。

```kusto
todatetime("2015-12-24") == datetime(2015-12-24)
```

**構文**

`todatetime(`*With*`)`

**引数**

* *Expr*: [datetime](./scalar-data-types/datetime.md)に変換される式。

**戻り値**

変換が成功した場合、結果は[datetime](./scalar-data-types/datetime.md)値になります。
それ以外の場合、結果は null になります。
 
> [!NOTE]
> 可能な場合は、 [datetime ()](./scalar-data-types/datetime.md)を使用することをお勧めします。
