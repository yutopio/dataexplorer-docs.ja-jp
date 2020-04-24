---
title: トップオペレーター - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのトップ オペレーターについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bb1d24dd0cd1dfeecb1925e474eb77e8cb86121f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505918"
---
# <a name="top-operator"></a>top 演算子

指定された列で並べ替えられた、先頭の *N* 個のレコードを返します。

```kusto
T | top 5 by Name desc nulls last
```

**構文**

*T* `| top`*番号の行*`by`*の式*[`asc` | `desc`] [`nulls first` | `nulls last`] ]

**引数**

* *行数*: 返す*T*の行数。 任意の数値式を指定できます。
* *式*: 並べ替えに使用するスカラー式。 値の型は、数値、日付、時刻、または文字列にする必要があります。
* `asc` または `desc` (既定値) は、実際には選択が範囲の "下限" と "上限" のどちらから行われるかを制御します。
* `nulls first`(順序の`asc`デフォルト)または`nulls last`(順序の`desc`デフォルト)は、NULL値が範囲の先頭または末尾のどちらになるかを制御するために表示されます。


**ヒント**

* `top 5 by name`セマンティックとパフォーマンスの`sort by name | take 5`両方の観点から、式と同等です。
* [階層 (入れ子になった](topnestedoperator.md)) 上位の結果を生成するには、top-nested 演算子を使用します。