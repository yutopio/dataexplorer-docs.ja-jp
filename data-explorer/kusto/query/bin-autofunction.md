---
title: bin_auto() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで bin_auto() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ebb214ae6a2676bf59a37e1e4e9cc3c085374bb3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517835"
---
# <a name="bin_auto"></a>bin_auto()

クエリ プロパティによって提供されるビン サイズと開始点を制御して、値を固定サイズの "bin" に切り捨てます。

**構文**

`bin_auto``(`*式*`)`

**引数**

* *式*: 丸める値を示す数値型のスカラー式。

**クライアント要求のプロパティ**

* `query_bin_auto_size`: 各ビンのサイズを示す数値リテラル。
* `query_bin_auto_at`: "固定点" である*Expression*の 1 つの値 (つまり、`fixed_point`値`bin_auto(fixed_point)`==`fixed_point`を示す数値リテラル)

**戻り値**

式の下の`query_bin_auto_at`最*Expression*も近い倍数`query_bin_auto_at`は、それ自体に翻訳されるようにシフトしました。

**使用例**

```kusto
set query_bin_auto_size=1h;
set query_bin_auto_at=datetime(2017-01-01 00:05);
range Timestamp from datetime(2017-01-01 00:05) to datetime(2017-01-01 02:00) step 1m
| summarize count() by bin_auto(Timestamp)
```

|Timestamp                    | count_|
|-----------------------------|-------|
|2017-01-01 00:05:00.0000000  | 60    |
|2017-01-01 01:05:00.0000000  | 56    |