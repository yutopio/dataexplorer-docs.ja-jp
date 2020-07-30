---
title: min_of ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの min_of () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ed8d14a4e793f253342c1b52269678874c96660f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346764"
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
