---
title: getyear() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで getyear() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a0e9d4c3e8c793f7775154261febc11e58082132
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514333"
---
# <a name="getyear"></a>getyear()

引数の年の部分を`datetime`返します。

**例**

```kusto
T
| extend year = getyear(datetime(2015-10-12))
// year == 2015
```