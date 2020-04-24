---
title: not() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの not() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 539e409a9e922afc390b097c863146b7fc30d7b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512038"
---
# <a name="not"></a>not()

引数の値を`bool`反転します。

```kusto
not(false) == true
```

**構文**

`not(`*Expr*`)`

**引数**

* *expr*:`bool`逆にする式。

**戻り値**

引数の逆の論理値を`bool`返します。