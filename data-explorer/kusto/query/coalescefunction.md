---
title: 合体 ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの合体 () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ea57efe36fb86189d798e5f18fa3fe9470bfd634
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227538"
---
# <a name="coalesce"></a>coalesce()

式のリストを評価し、null 以外の最初の式 (文字列の場合は空でない) を返します。

```kusto
coalesce(tolong("not a number"), tolong("42"), 33) == 42
```

**構文**

`coalesce(`*expr_1* `, `*expr_2* `,`...)

**引数**

* *expr_i*: 評価されるスカラー式。
- すべての引数は同じ型である必要があります。
- 最大64個の引数がサポートされています。


**戻り値**

値が null でない (文字列式の場合は空ではない) 最初の*expr_i*の値。

**例**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=coalesce(tolong("not a number"), tolong("42"), 33)
```

|結果|
|---|
|42|
