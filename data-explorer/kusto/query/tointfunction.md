---
title: toint ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの toint () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1efd06b8fa2961528be65b630933aae74614e9a0
ms.sourcegitcommit: 0d15903613ad6466d49888ea4dff7bab32dc5b23
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "86013608"
---
# <a name="toint"></a>toint()

入力を整数 (符号付き32ビット) の数値表現に変換します。

```kusto
toint("123") == int(123)
```

**構文**

`toint(`*With*`)`

**引数**

* *Expr*: 整数に変換される式。 

**戻り値**

変換が成功した場合、結果は整数になります。
変換が成功しなかった場合、結果はになり `null` ます。
 
*注*: 可能な限り[int ()](./scalar-data-types/int.md)を使用することをお勧めします。
