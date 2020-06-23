---
title: rolling_percentile プラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの rolling_percentile プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0ff4ad4adbae580e34c946eb9d18ca3337d3c49c
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128888"
---
# <a name="rolling_percentile-plugin"></a>rolling_percentile () プラグイン

ビン*サイズ*ごとのローリング (スライディング) ビン*sperwindow*サイズウィンドウでの、指定された値の百分位数*の母集団を*返します。

```kusto
T | evaluate rolling_percentile(ValueColumn, Percentile, IndexColumn, BinSize, BinsPerWindow)
```

**構文**

*T* `| evaluate` `rolling_percentile(` *valuecolumn* `,` *センタイル* `,` *indexcolumn* `,` *binsize* `,` *binsperwindow* [ `,` *dim1* `,` *dim2* `,` ...]`)`

**引数**

* *T*: 入力テーブル式。
* *Valuecolumn*: 百分位を計算する値を持つ列の名前。 
* *百分位*: 計算する百分位のスカラー。
* *Indexcolumn*: ローリングウィンドウを実行する列の名前。
* *Binsize*: *indexcolumn*に適用するビンのサイズを含むスカラー。
* *Binsperwindow*: 各ウィンドウに含まれるビン数を含むスカラー。
* *dim1*, *dim2*,...: (省略可能) スライスするディメンション列の一覧。

**戻り値**

各ビン (および指定されている場合は、次元の組み合わせ) ごとに1行のテーブルを返します。このテーブルには、ビンで終わるウィンドウ内の値の割合がロールアウトされます。 個別のカウント値、新しい値の個別の数、時間枠ごとの集計された個別のカウント。

出力テーブルスキーマは次のとおりです。


|IndexColumn|dim1|...|dim_n|rolling_BinsPerWindow_percentile_ValueColumn_Pct
|---|---|---|---|---|


**使用例**

### <a name="rolling-3-day-median-value-per-day"></a>1日に3日の中央値をローリングする 

次のクエリでは、毎日の粒度で3日の中央値を計算します。 出力の各行は、ビン自体を含む最後の3つのビンの中央値を表します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let T = 
range idx from 0 to 24*10-1 step 1
| project Timestamp = datetime(2018-01-01) + 1h*idx, val=idx+1
| extend EvenOrOdd = iff(val % 2 == 0, "Even", "Odd");
 T  
 | evaluate rolling_percentile(val, 50, Timestamp, 1d, 3)
```

|Timestamp|rolling_3_percentile_val_50|
|---|---|
|2018-01-01 00:00: 00.0000000|   12|
|2018-01-02 00:00: 00.0000000|   24|
|2018-01-03 00:00: 00.0000000|   36|
|2018-01-04 00:00: 00.0000000|   60|
|2018-01-05 00:00: 00.0000000|   84|
|2018-01-06 00:00: 00.0000000|   108|
|2018-01-07 00:00: 00.0000000|   132|
|2018-01-08 00:00: 00.0000000|   156|
|2018-01-09 00:00: 00.0000000|   180|
|2018-01-10 00:00: 00.0000000|   204|

### <a name="rolling-3-day-median-value-per-day-by-dimension"></a>ディメンションごとの1日あたりの3日の平均値のローリング

上記の例と同じですが、ディメンションの値ごとにパーティション分割されたローリングウィンドウも計算します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let T = 
range idx from 0 to 24*10-1 step 1
| project Timestamp = datetime(2018-01-01) + 1h*idx, val=idx+1
| extend EvenOrOdd = iff(val % 2 == 0, "Even", "Odd");
 T  
 | evaluate rolling_percentile(val, 50, Timestamp, 1d, 3, EvenOrOdd)
```

|Timestamp| EvenOrOdd|  rolling_3_percentile_val_50|
|---|---|---|
|2018-01-01 00:00: 00.0000000|   さえ|   12|
|2018-01-02 00:00: 00.0000000|   さえ|   24|
|2018-01-03 00:00: 00.0000000|   さえ|   36|
|2018-01-04 00:00: 00.0000000|   さえ|   60|
|2018-01-05 00:00: 00.0000000|   さえ|   84|
|2018-01-06 00:00: 00.0000000|   さえ|   108|
|2018-01-07 00:00: 00.0000000|   さえ|   132|
|2018-01-08 00:00: 00.0000000|   さえ|   156|
|2018-01-09 00:00: 00.0000000|   さえ|   180|
|2018-01-10 00:00: 00.0000000|   さえ|   204|
|2018-01-01 00:00: 00.0000000|   ページ|    11|
|2018-01-02 00:00: 00.0000000|   ページ|    23|
|2018-01-03 00:00: 00.0000000|   ページ|    35|
|2018-01-04 00:00: 00.0000000|   ページ|    59|
|2018-01-05 00:00: 00.0000000|   ページ|    83|
|2018-01-06 00:00: 00.0000000|   ページ|    107|
|2018-01-07 00:00: 00.0000000|   ページ|    131|
|2018-01-08 00:00: 00.0000000|   ページ|    155|
|2018-01-09 00:00: 00.0000000|   ページ|    179|
|2018-01-10 00:00: 00.0000000|   ページ|    203|
