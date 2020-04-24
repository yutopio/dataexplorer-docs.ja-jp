---
title: isnotnull() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで isnotnull() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8be57f2f7484081ac316a8af6fea252a60f895a4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513449"
---
# <a name="isnotnull"></a>を返す

引数`true`が null でない場合に返します。

**構文**

`isnotnull(`[*値*]`)`

`notnull(`[*値*]`)` - エイリアスの`isnotnull`

**例**

```kusto
T | where isnotnull(PossiblyNull) | count
```

上記の結果を得る方法はほかにもあることに注意してください。

```kusto
T | summarize count(PossiblyNull)
```