---
title: max_of ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの max_of () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c2045436de09bc31fa0378824310fa872478b861
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346832"
---
# <a name="max_of"></a>max_of()

複数の評価された数値式の最大値を返します。

```kusto
max_of(10, 1, -3, 17) == 17
```

## <a name="syntax"></a>構文

`max_of``(` *expr_1* `,` *expr_2*を expr_1 しています...`)`

## <a name="arguments"></a>引数

* *expr_i*: 評価されるスカラー式。

- すべての引数は同じ型である必要があります。
- 最大64個の引数がサポートされています。

## <a name="returns"></a>戻り値

すべての引数式の最大値。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result = max_of(10, 1, -3, 17) 
```

|結果|
|---|
|17|
