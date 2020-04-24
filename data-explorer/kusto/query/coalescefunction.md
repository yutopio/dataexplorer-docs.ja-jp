---
title: coalesce() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの coalesce() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1ee2cd24f36007914fdc326e2863da148aec4406
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517138"
---
# <a name="coalesce"></a>coalesce()

式のリストを評価し、最初の非 null (または文字列の場合は空でない) 式を返します。

```kusto
coalesce(tolong("not a number"), tolong("42"), 33) == 42
```

**構文**

`coalesce(`*expr_1*`, `*expr_1expr_2.)* `,`

**引数**

* *expr_i*: 評価されるスカラー式。
- すべての引数は同じ型である必要があります。
- 最大 64 個の引数がサポートされます。


**戻り値**

値が null ではない (または文字列式の場合は空ではない) 最初の*expr_i*の値。

**例**

```kusto
print result=coalesce(tolong("not a number"), tolong("42"), 33)
```

|結果|
|---|
|42|