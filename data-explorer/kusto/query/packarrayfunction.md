---
title: pack_array() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの pack_array() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ad13efd6b0743a3c092b2859ea032c3731ffdf1b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511868"
---
# <a name="pack_array"></a>pack_array()

すべての入力値を動的配列にパックします。

**構文**

`pack_array(`*エクスプル1*`[`` *Expr2*]`、)'

**引数**

* *Expr1..N*: 動的配列にパックされる入力式。

**戻り値**

Expr1、Expr2、...、ExprN の値を含む動的配列。

**例**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project pack_array(x,y,z)
```

|Column1|
|---|
|[1,2,4]|
|[2,4,8]|
|[3,6,12]|

```kusto
range x from 1 to 3 step 1
| extend y = tostring(x * 2)
| extend z = (x * 2) * 1s
| project pack_array(x,y,z)
```

|Column1|
|---|
|[1,"2","00:00:02"]|
|[2,"4","00:00:04"]|
|[3,"6","00:00:06"]|