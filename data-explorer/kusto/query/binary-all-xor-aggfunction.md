---
title: binary_all_xor() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでbinary_all_xor() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: a1908fe874576281c9ba45f23709845473b5725c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517699"
---
# <a name="binary_all_xor-aggregation-function"></a>binary_all_xor() (集計関数)

集計グループごとのバイナリ`XOR`演算を使用して値を累積します (または、グループ化なしで集計が行われた場合は合計で)。

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`binary_all_xor(`*エクスプの*要約`)`

**引数**

* *Expr*: 長い数字。

**戻り値**

集計グループごとのレコードに対するバイナリ`XOR`演算を使用して集計された値を返します (集計がグループ化なしで行われた場合は合計で返します)。

**例**

バイナリ`XOR`操作を使用して「カフェフード」を生産する:

```kusto
datatable(num:long)
[
  0x44404440,
  0x1E1E1E1E,
  0x90ABBA09,
  0x000B105A,
]
| summarize result = toupper(tohex(binary_all_xor(num)))
```

|結果|
|---|
|カフェフ00D|