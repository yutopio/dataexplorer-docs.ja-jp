---
title: toint() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで toint() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 570e13dc816c8a7e6d5baa488912fd8def5d2883
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506105"
---
# <a name="toint"></a>toint()

入力を整数 (符号付き 32 ビット) 数値表現に変換します。

```kusto
toint("123") == 123
```

**構文**

`toint(`*Expr*`)`

**引数**

* *Expr*: 整数に変換される式。 

**戻り値**

変換が成功すると、結果は整数になります。
変換が成功しなかった場合は、 が`null`返されます。
 
*注意*: 可能な場合[は int() を](./scalar-data-types/int.md)使用することをお好みで指定します。