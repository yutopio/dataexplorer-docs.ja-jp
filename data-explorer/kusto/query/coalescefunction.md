---
title: 合体 ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの合体 () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3eb5e533c2b4430f54909507e521912711c35811
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252646"
---
# <a name="coalesce"></a>coalesce()

式のリストを評価し、null 以外の最初の式 (文字列の場合は空でない) を返します。

```kusto
coalesce(tolong("not a number"), tolong("42"), 33) == 42
```

## <a name="syntax"></a>構文

`coalesce(`*expr_1* `, `*expr_2* `,`...)

## <a name="arguments"></a>引数

* *expr_i*: 評価されるスカラー式。
- すべての引数は同じ型である必要があります。
- 最大64個の引数がサポートされています。


## <a name="returns"></a>戻り値

値が null でない (文字列式の場合は空ではない) 最初の *expr_i* の値。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=coalesce(tolong("not a number"), tolong("42"), 33)
```

|結果|
|---|
|42|
