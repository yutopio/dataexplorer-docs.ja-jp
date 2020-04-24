---
title: binary_or() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの binary_or() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1f6d68137ded3b164ca25d82e0c17b6fef6fc1a1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517597"
---
# <a name="binary_or"></a>binary_or()

2 つの値のビットごとの`or`演算の結果を返します。 

```kusto
binary_or(x,y)
```

**構文**

`binary_or(`*num1* `,` *num2*`)`

**引数**

* *num1* *、num2*: 長い数値。

**戻り値**

数値のペアに対する論理 OR 演算を返します: num1 |num2.