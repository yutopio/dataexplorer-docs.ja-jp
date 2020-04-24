---
title: binary_not() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで binary_not() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ae808d3d9964b8e63053ed40d65d08f39adf6668
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517665"
---
# <a name="binary_not"></a>binary_not()

入力値のビットごとの否定を返します。

```kusto
binary_not(x)
```

**構文**

`binary_not(`*num1*`)`

**引数**

* *num1*: 数値 

**戻り値**

数値 num1 に対する論理 NOT 演算を返します。