---
title: min_of() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで min_of() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 06a8f7ce6bcef8f3c15c4ea3d4c997b4e4540bf7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512395"
---
# <a name="min_of"></a>min_of()

評価された複数の数値式の最小値を返します。

```kusto
min_of(10, 1, -3, 17) == -3
```

**構文**

`min_of``(` *expr_1* `,` *expr_1expr_2..*`)`

**引数**

* *expr_i*: 評価されるスカラー式。

- すべての引数は同じ型である必要があります。
- 最大 64 個の引数がサポートされます。

**戻り値**

すべての引数式の最小値。

**例**

```kusto
print result=min_of(10, 1, -3, 17) 
```

|結果|
|---|
|-3|