---
title: rolling_percentileプラグイン - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーrolling_percentileプラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 02def4069c83eeec080ca059493132619fce30d5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510270"
---
# <a name="rolling_percentile-plugin"></a>rolling_percentileプラグイン

BinSize ごとのビン*の*サイズ ウィンドウをローリング (スライディング) で *、ValueColumn*の母集団の*BinSize*指定された百分位数の推定値を返します。

```kusto
T | evaluate rolling_percentile(ValueColumn, Percentile, IndexColumn, BinSize, BinsPerWindow)
```

**構文**

*T*`| evaluate``,``,``,``,``,`*ValueColumn*`,``,`*BinSize**IndexColumn**Percentile**dim1**dim2**BinsPerWindow*値列百分位数インデックス列 ビンサイズ ビンズパーウィンドウ [dim1 dim2.]] `rolling_percentile(``)`

**引数**

* *T*: 入力表形式の式。
* *ValueColumn*: 百分位数を計算する値を持つ列の名前。 
* *百分位*数 : 計算するパーセンタイルを持つスカラー。
* *インデックス列*: ローリング ウィンドウを実行する列の名前。
* *BinSize*:*インデックスカラム*に適用するビンのサイズを持つスカラー。
* *ビンスペルウィンドウ*: 各ウィンドウに含まれるビンの数を持つスカラー。
* *dim1*、 *dim2*、 . : (オプション) スライスするディメンション列のリスト。

**戻り値**

各ビン (および指定されている場合はディメンションの組み合わせ) ごとに、ビンで終わるウィンドウ内の値のローリングパーセンタイルを持つテーブルを返します (含む)。 個別のカウント値、新しい値の個別のカウント、各タイム ウィンドウの集計済み個別カウント。

出力テーブルスキーマは次のとおりです。


|インデックス列|薄暗い1|...|dim_n|rolling_BinsPerWindow_percentile_ValueColumn_Pct
|---|---|---|---|---|


**使用例**

### <a name="rolling-3-day-median-value-per-day"></a>1 日あたりのローリング 3 日の中央値 

次のクエリでは、日単位の粒度で 3 日の中央値を計算します。 出力の各行は、ビン自体を含む、最後の 3 つのビン (日) の中央値を表します。

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
|2018-01-01 00:00:00.0000000|   12|
|2018-01-02 00:00:00.0000000|   24|
|2018-01-03 00:00:00.0000000|   36|
|2018-01-04 00:00:00.0000000|   60|
|2018-01-05 00:00:00.0000000|   84|
|2018-01-06 00:00:00.0000000|   108|
|2018-01-07 00:00:00.0000000|   132|
|2018-01-08 00:00:00.0000000|   156|
|2018-01-09 00:00:00.0000000|   180|
|2018-01-10 00:00:00.0000000|   204|

### <a name="rolling-3-day-median-value-per-day-by-dimension"></a>ディメンション別の 1 日あたりのローリング 3 日の中央値

上の例と同じですが、ディメンションの値ごとに分割されたローリング ウィンドウも計算されるようになりました。

```kusto
let T = 
range idx from 0 to 24*10-1 step 1
| project Timestamp = datetime(2018-01-01) + 1h*idx, val=idx+1
| extend EvenOrOdd = iff(val % 2 == 0, "Even", "Odd");
 T  
 | evaluate rolling_percentile(val, 50, Timestamp, 1d, 3, EvenOrOdd)
```

|Timestamp| エヴェンオーロッド|  rolling_3_percentile_val_50|
|---|---|---|
|2018-01-01 00:00:00.0000000|   も|   12|
|2018-01-02 00:00:00.0000000|   も|   24|
|2018-01-03 00:00:00.0000000|   も|   36|
|2018-01-04 00:00:00.0000000|   も|   60|
|2018-01-05 00:00:00.0000000|   も|   84|
|2018-01-06 00:00:00.0000000|   も|   108|
|2018-01-07 00:00:00.0000000|   も|   132|
|2018-01-08 00:00:00.0000000|   も|   156|
|2018-01-09 00:00:00.0000000|   も|   180|
|2018-01-10 00:00:00.0000000|   も|   204|
|2018-01-01 00:00:00.0000000|   奇数|    11|
|2018-01-02 00:00:00.0000000|   奇数|    23|
|2018-01-03 00:00:00.0000000|   奇数|    35|
|2018-01-04 00:00:00.0000000|   奇数|    59|
|2018-01-05 00:00:00.0000000|   奇数|    83|
|2018-01-06 00:00:00.0000000|   奇数|    107|
|2018-01-07 00:00:00.0000000|   奇数|    131|
|2018-01-08 00:00:00.0000000|   奇数|    155|
|2018-01-09 00:00:00.0000000|   奇数|    179|
|2018-01-10 00:00:00.0000000|   奇数|    203|