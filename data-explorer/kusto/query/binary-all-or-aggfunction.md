---
title: binary_all_or() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで binary_all_or() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 5de240f606e53b26996ebfe11073170758233e2c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517716"
---
# <a name="binary_all_or-aggregation-function"></a>binary_all_or() (集計関数)

集計グループごとのバイナリ`OR`演算を使用して値を累積します (または、グループ化なしで集計が行われた場合は合計で)。

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`binary_all_or(`*エクスプの*要約`)`

**引数**

* *Expr*: 長い数字。

**戻り値**

集計グループごとのレコードに対するバイナリ`OR`演算を使用して集計された値を返します (集計がグループ化なしで行われた場合は合計で返します)。

**例**

バイナリ`OR`操作を使用して「カフェフード」を生産する:

```kusto
datatable(num:long)
[
  0x88888008,
  0x42000000,
  0x00767000,
  0x00000005, 
]
| summarize result = toupper(tohex(binary_all_or(num)))
```

|結果|
|---|
|カフェフ00D|