---
title: series_stats_dynamic() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで series_stats_dynamic() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: b667af6d037b0b316bf18406e1fb49528e390213
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507907"
---
# <a name="series_stats_dynamic"></a>series_stats_dynamic()

動的オブジェクト内の系列の統計情報を返します。  

この`series_stats_dynamic()`関数は、動的な数値配列を含む列を入力として受け取り、次の内容の動的な値を生成します。
* `min`: 入力配列の最小値
* `min_idx`: 入力配列の最小値の最初の位置
* `max`: 入力配列の最大値
* `max_idx`: 入力配列の最大値の最初の位置
* `avg`: 入力配列の平均値
* `variance`: 入力配列の標本分散
* `stdev`: 入力配列のサンプル標準偏差

**構文**

`series_stats_dynamic(`*x* `[,` *ignore_nonfinite*`])`

**引数**

* *x*: 数値の配列である動的配列セル。 
* *ignore_nonfinite*: 非有限値`false`*(null、NaN* *、inf*など) を無視して統計を計算するかどうかを指定*NaN*するブール型 (オプション、デフォルト: ) フラグ。 返される結果`false`に設定されている場合`null`は、配列内に非有限値が存在するかどうか。

**例**

```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project stats=series_stats_dynamic(x)
```

|stats
|---|
|{"min", "min_idx" : 8, "max": 87.0, "max_idx" : 3, "平均" : "32.8" "stdev": 28.503633853548269, "分散": 812.45714285714291}