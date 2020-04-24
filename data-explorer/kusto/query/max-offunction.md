---
title: max_of() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで max_of() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 68188ccd5eb814a22be166b8847d80193172813f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512497"
---
# <a name="max_of"></a>max_of()

評価された複数の数値式の最大値を返します。

```kusto
max_of(10, 1, -3, 17) == 17
```

**構文**

`max_of``(` *expr_1* `,` *expr_1expr_2..*`)`

**引数**

* *expr_i*: 評価されるスカラー式。

- すべての引数は同じ型である必要があります。
- 最大 64 個の引数がサポートされます。

**戻り値**

すべての引数式の最大値。

**例**

```kusto
print result = max_of(10, 1, -3, 17) 
```

|結果|
|---|
|17|