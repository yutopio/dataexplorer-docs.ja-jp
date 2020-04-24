---
title: activity_counts_metricsプラグイン - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーactivity_counts_metricsプラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e55cd940850440499d227082f57769e499e6a551
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519314"
---
# <a name="activity_counts_metrics-plugin"></a>activity_counts_metrics プラグイン

すべての*前の*タイム ウィンドウと比較/集計された各タイム ウィンドウの有用なアクティビティ メトリック (合計カウント値、個別のカウント、個別の値、集計された個別のカウント) を計算します (各タイム ウィンドウが以前の時間枠と比較される[activity_metricsプラグイン](activity-metrics-plugin.md)とは異なります)。

```kusto
T | evaluate activity_counts_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**構文**

*T* `| evaluate` `,` *Start* `,` `,` *Window* `,` `,` `,` *IdColumn* `,` `,` *dim2* *End* *TimelineColumn* *Cohort* *dim1* IdColumn タイムライン列開始ウィンドウ [ コホート ] [ dim1 dim2 ..] `activity_counts_metrics(`[`,` *ルックバック*]`)`

**引数**

* *T*: 入力表形式の式。
* *IdColumn*: ユーザー アクティビティを表す ID 値を持つ列の名前。 
* *タイムライン列*: タイムラインを表す列の名前。
* *開始*: 分析開始期間の値を持つスカラー。
* *終了*: 分析終了期間の値を持つスカラー。
* *ウィンドウ*: 分析ウィンドウ期間の値を持つスカラー。 数値/日時/タイムスタンプ値、またはの 1 つである[startofweek](startofweekfunction.md)/[startofmonth](startofmonthfunction.md)/[startofyear](startofyearfunction.md)`week`/`month`/`year`文字列を指定できます。 
* *dim1*、 *dim2*、 .: (オプション) アクティビティ指標の計算をスライスするディメンション列のリスト。

**戻り値**

合計カウント値、個別のカウント値、新しい値の個別のカウント、各タイム ウィンドウの集計された個別カウントを持つテーブルを返します。

出力テーブルスキーマは次のとおりです。

|タイムライン列|薄暗い1|...|dim_n|count|dcount|new_dcount|aggregated_dcount
|---|---|---|---|---|---|---|---|---|
|タイプ:*タイムライン列の時点*|..|..|..|long|long|long|long|long


* *タイムライン列*: タイム ウィンドウの開始時刻。
* *カウント*: タイム ウィンドウと dim*の*レコード数の合計
* *dcount*: 個別の ID 値は、タイム ウィンドウと*dim でカウントされます。*
* *new_dcount*: タイム ウィンドウと dim*の*ID 値を、以前のすべてのタイム ウィンドウと比較して異なります。 
* *aggregated_dcount*: 1 番目のタイム ウィンドウから現在の (含む) までの*dim(s) の*合計の個別 ID 値。

**使用例**

### <a name="daily-activity-counts"></a>毎日の活動数 

次のクエリは、指定された入力テーブルの毎日のアクティビティ数を計算します

```kusto
let start=datetime(2017-08-01);
let end=datetime(2017-08-04);
let window=1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'A', datetime(2017-08-01),
'D', datetime(2017-08-01), 
'J', datetime(2017-08-01),
'B', datetime(2017-08-01),
'C', datetime(2017-08-02),  
'T', datetime(2017-08-02),
'J', datetime(2017-08-02),
'H', datetime(2017-08-03),
'T', datetime(2017-08-03),
'T', datetime(2017-08-03),
'J', datetime(2017-08-03),
'B', datetime(2017-08-03),
'S', datetime(2017-08-03),
'S', datetime(2017-08-04),
];
 T 
 | evaluate activity_counts_metrics(UserId, Timestamp, start, end, window)
```

|Timestamp|count|dcount|new_dcount|aggregated_dcount|
|---|---|---|---|---|
|2017-08-01 00:00:00.0000000|4|4|4|4|
|2017-08-02 00:00:00.0000000|3|3|2|6|
|2017-08-03 00:00:00.0000000|6|5|2|8|
|2017-08-04 00:00:00.0000000|1|1|0|8|


