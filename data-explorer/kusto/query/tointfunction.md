---
title: toint ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの toint () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7f0ede908be2689165f641038b2b6f699c0eb543
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550607"
---
# <a name="toint"></a>toint()

入力を整数 (符号付き32ビット) の数値表現に変換します。

```kusto
toint("123") == 123s
```

**構文**

`toint(`*With*`)`

**引数**

* *Expr*: 整数に変換される式。 

**戻り値**

変換が成功した場合、結果は整数になります。
変換が成功しなかった場合、結果はになり `null` ます。
 
*注*: 可能な限り[int ()](./scalar-data-types/int.md)を使用することをお勧めします。