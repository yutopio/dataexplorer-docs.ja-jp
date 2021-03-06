---
title: iff ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの iff () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5cc6a41c8b74e4fd08eebbe968b7384dce39039e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252287"
---
# <a name="iff"></a>iff()

1番目の引数 (述語) を評価し、述語が (second) に評価されたか、または (3 番目の) に評価されたかに応じて、2番目または3番目の引数の値を返し `true` `false` ます。

2 番目と 3 番目の引数は、同じ型である必要があります。

## <a name="syntax"></a>構文

`iff(`*述語* `,`*Iftrue* `,`*ifFalse*`)`

## <a name="arguments"></a>引数

* *述語*: 値に評価される式 `boolean` 。
* *Iftrue*: *述語* がと評価された場合に、評価され、その値が関数から返される `true` 式。
* *ifFalse*: *述語* がと評価された場合に、評価され、その値が関数から返される `false` 式。

## <a name="returns"></a>戻り値

この関数は、*predicate* が `true` と評価された場合に *ifTrue* の値を返し、それ以外の場合に *ifFalse* の値を返します。

## <a name="example"></a>例

```kusto
T 
| extend day = iff(floor(Timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```