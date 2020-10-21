---
title: min_of ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの min_of () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c08ef6f147640330cd5ea33c55dcc6acafd77a31
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248824"
---
# <a name="min_of"></a>min_of()

複数の評価された数値式の最小値を返します。

```kusto
min_of(10, 1, -3, 17) == -3
```

## <a name="syntax"></a>構文

`min_of``(` *expr_1* `,` *expr_2*を expr_1 しています...`)`

## <a name="arguments"></a>引数

* *expr_i*: 評価されるスカラー式。

- すべての引数は同じ型である必要があります。
- 最大64個の引数がサポートされています。

## <a name="returns"></a>戻り値

すべての引数式の最小値。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=min_of(10, 1, -3, 17) 
```

|結果|
|---|
|-3|
