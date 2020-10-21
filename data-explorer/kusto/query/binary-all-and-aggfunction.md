---
title: binary_all_and () (集計関数)-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの binary_all_and () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: ffc143a3e9e76ab2d822754cc5488c0c830eef89
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245410"
---
# <a name="binary_all_and-aggregation-function"></a>binary_all_and () (集計関数)

概要作成グループごとにバイナリ演算を使用して値を累積し `AND` ます (または、グループ化せずに概要作成を実行する場合は合計)。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>構文

集計の `binary_all_and(` *Expr*`)`

## <a name="arguments"></a>引数

* *Expr*: long 数値。

## <a name="returns"></a>戻り値

集計グループごとのレコードに対するバイナリ演算を使用して集計された値を返し `AND` ます (集約がグループ化されていない場合は合計で)。

## <a name="example"></a>例

バイナリ演算を使用して ' カフェ ' を作成してい `AND` ます:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(num:long)
[
  0xFFFFFFFF, 
  0xFFFFF00F,
  0xCFFFFFFD,
  0xFAFEFFFF,
]
| summarize result = toupper(tohex(binary_all_and(num)))
```

|結果|
|---|
|CAFEF00D|
