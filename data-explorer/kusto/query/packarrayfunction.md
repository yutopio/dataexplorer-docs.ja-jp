---
title: pack_array ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの pack_array () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ad6403a70a8f71eee05bc56789cae2d6287a6605
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346526"
---
# <a name="pack_array"></a>pack_array()

すべての入力値を動的配列にパックします。

## <a name="syntax"></a>構文

`pack_array(`*Expr1 or* `[` 、 ` *Expr2*]` ) '

## <a name="arguments"></a>引数

* *Expr1 or...N*: 動的配列にパックされる入力式。

## <a name="returns"></a>戻り値

Expr1 or、Expr2、ExprN の値を含む動的配列。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project pack_array(x,y,z)
```

|Column1|
|---|
|[1, 2, 4]|
|[2, 4, 8]|
|[3、6、12]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = tostring(x * 2)
| extend z = (x * 2) * 1s
| project pack_array(x,y,z)
```

|Column1|
|---|
|[1, "2", "00:00:02"]|
|[2、"4"、"00:00:04"]|
|[3、"6"、"00:00:06"]|
