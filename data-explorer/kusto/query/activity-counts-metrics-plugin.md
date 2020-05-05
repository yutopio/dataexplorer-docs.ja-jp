---
title: activity_counts_metrics プラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの activity_counts_metrics プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b06b1c137552ba19f9b1ef5367a25bb72eea5c93
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82742039"
---
# <a name="activity_counts_metrics-plugin"></a>activity_counts_metrics プラグイン

は、時間枠を比較したり、過去の*すべて*の時間枠に集計したりするときに便利なアクティビティメトリックを計算します。 メトリックには、合計数の値、個別のカウント値、個別の新しい値の数、集計された個別のカウントがあります。 このプラグインを[activity_metrics プラグイン](activity-metrics-plugin.md)と比較します。このプラグインでは、すべての時間枠が前の時間枠と比較されます。

```kusto
T | evaluate activity_counts_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**構文**

*T* `| evaluate` `,` *TimelineColumn* `,` *dim2* *End* `,` *Window* `,` *dim1* *IdColumn* `,` *Start* `,` *Cohort*idcolumn TimelineColumn Start`,` End`,`ウィンドウ [cohort] [dim1 dim2...] `activity_counts_metrics(`[`,` *元に戻す*]`)`

**引数**

* *T*: 入力テーブル式。
* *Idcolumn*: ユーザーアクティビティを表す ID 値を持つ列の名前。 
* *TimelineColumn*: タイムラインを表す列の名前。
* *Start*: 分析の開始期間の値を使用したスカラー。
* *End*: 分析終了期間の値を使用したスカラー。
* *ウィンドウ*: 分析ウィンドウ期間の値を持つスカラー。 には、数値/日付/時刻/タイムスタンプ値、またはのいずれか`week` / `month` / `year`の文字列を指定できます。この場合、すべての期間は[startofweek](startofweekfunction.md)/[startofmonth](startofmonthfunction.md)または[startofyear](startofyearfunction.md)になります。 
* *dim1*、 *dim2*、...: (省略可能) アクティビティメトリックスの計算をスライスするディメンション列の一覧です。

**戻り値**

合計カウント値、個別のカウント値、個別の値の数、および時間枠ごとの集計された個別のカウントを含むテーブルを返します。

出力テーブルスキーマは次のとおりです。

|`TimelineColumn`|`dim1`|...|`dim_n`|`count`|`dcount`|`new_dcount`|`aggregated_dcount`
|---|---|---|---|---|---|---|---|---|
|型: 次の as*`TimelineColumn`*|..|..|..|long|long|long|long|long


* *`TimelineColumn`*: 時間枠の開始時刻。
* *`count`*: 時間枠と*dim*の合計レコード数
* *`dcount`*: 時間枠と*dim*の個別の ID 値の数
* *`new_dcount`*: 時間枠内の個別の ID 値と、以前のすべての時間*枠との*差異。 
* *`aggregated_dcount`*: 最初の時間枠から現在までの*dim*の個別の ID 値の合計 (を含む)。

**使用例**

### <a name="daily-activity-counts"></a>日単位のアクティビティ数 

次のクエリでは、指定された入力テーブルの日単位のアクティビティ数を計算します。

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

|`Timestamp`|`count`|`dcount`|`new_dcount`|`aggregated_dcount`|
|---|---|---|---|---|
|2017-08-01 00:00: 00.0000000|4|4|4|4|
|2017-08-02 00:00: 00.0000000|3|3|2|6|
|2017-08-03 00:00: 00.0000000|6|5|2|8|
|2017-08-04 00:00: 00.0000000|1|1|0|8|


