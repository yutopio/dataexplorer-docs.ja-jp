---
title: sliding_window_countsプラグイン - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーsliding_window_countsプラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: feab3d0e8f548817be12f202eb2d494bd65aa133
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507499"
---
# <a name="sliding_window_counts-plugin"></a>sliding_window_countsプラグイン

[ここで](samples.md#performing-aggregations-over-a-sliding-window)説明する手法を使用して、スライディング ウィンドウ内の値の数と個別の数を計算します。

たとえば、各*日*に対して、前*の週*のユーザー数と個別のユーザー数を計算します。 

```kusto
T | evaluate sliding_window_counts(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, dim1, dim2, dim3)
```

**構文**

*T* `| evaluate` `,` *End* `,` *Bin* `,` *IdColumn* `,` `,` *Start* `,` `,` `,` *TimelineColumn* *LookbackWindow* *dim2* *dim1* IdColumn タイムライン列開始エンドルックバックウィンドウビン [dim1 dim2.] `sliding_window_counts(``)`

**引数**

* *T*: 入力表形式の式。
* *IdColumn*: ユーザー アクティビティを表す ID 値を持つ列の名前。 
* *タイムライン列*: タイムラインを表す列の名前。
* *開始*: 分析開始期間の値を持つスカラー。
* *終了*: 分析終了期間の値を持つスカラー。
* *ルックバックウィンドウ*: ルックバック期間のスカラー定数値 (過去 7d のユーザーの場合: ルックバックウィンドウ = 7d など)
* *Bin*: 分析ステップ期間のスカラー定数値。 数値/日時/タイムスタンプ値、またはの 1 つである[startofweek](startofweekfunction.md)/[startofmonth](startofmonthfunction.md)/[startofyear](startofyearfunction.md)`week`/`month`/`year`文字列を指定できます。 
* *dim1*、 *dim2*、 .: (オプション) アクティビティ指標の計算をスライスするディメンション列のリスト。

**戻り値**

ルックバック期間の Id のカウント値と個別のカウント値を持つテーブルを返します。

出力テーブルスキーマは次のとおりです。

|*タイムライン列*|薄暗い1|..|dim_n|Count|Dカウント|
|---|---|---|---|---|---|
|タイプ:*タイムライン列の時点*|..|..|..|long|long|


**使用例**

分析期間内の毎日の過去 1 週間のユーザーのカウントと dcounts を計算します。 

```kusto
let start = datetime(2017 - 08 - 01);
let end = datetime(2017 - 08 - 07); 
let lookbackWindow = 3d;  
let bin = 1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'Bob',      datetime(2017 - 08 - 01), 
'David',    datetime(2017 - 08 - 01), 
'David',    datetime(2017 - 08 - 01), 
'John',     datetime(2017 - 08 - 01), 
'Bob',      datetime(2017 - 08 - 01), 
'Ananda',   datetime(2017 - 08 - 02),  
'Atul',     datetime(2017 - 08 - 02), 
'John',     datetime(2017 - 08 - 02), 
'Ananda',   datetime(2017 - 08 - 03), 
'Atul',     datetime(2017 - 08 - 03), 
'Atul',     datetime(2017 - 08 - 03), 
'John',     datetime(2017 - 08 - 03), 
'Bob',      datetime(2017 - 08 - 03), 
'Betsy',    datetime(2017 - 08 - 04), 
'Bob',      datetime(2017 - 08 - 05), 
];
T | evaluate sliding_window_counts(UserId, Timestamp, start, end, lookbackWindow, bin)


```

|Timestamp|Count|Dカウント|
|---|---|---|
|2017-08-01 00:00:00.0000000|5|3|
|2017-08-02 00:00:00.0000000|8|5|
|2017-08-03 00:00:00.0000000|13|5|
|2017-08-04 00:00:00.0000000|9|5|
|2017-08-05 00:00:00.0000000|7|5|
|2017-08-06 00:00:00.0000000|2|2|
|2017-08-07 00:00:00.0000000|1|1|