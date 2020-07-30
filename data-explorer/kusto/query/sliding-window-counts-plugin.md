---
title: sliding_window_counts プラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの sliding_window_counts プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: af223d31f008b972bc1b61a6a9ace7e19c988ff7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351048"
---
# <a name="sliding_window_counts-plugin"></a>sliding_window_counts プラグイン

[ここで](samples.md#perform-aggregations-over-a-sliding-window)説明する手法を使用して、スライド式ウィンドウのカウントと個別の値の数を確認します。

たとえば、 *1 日*につき、前の*週*のユーザーのカウントと個別のカウントを計算します。 

```kusto
T | evaluate sliding_window_counts(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, dim1, dim2, dim3)
```

## <a name="syntax"></a>構文

*T* `| evaluate` `sliding_window_counts(` *idcolumn* `,` *TimelineColumn* `,` *Start* `,` *End* `,` *look backwindow* `,` *Bin* `,` [*dim1* `,` *dim2* `,` ...]`)`

## <a name="arguments"></a>引数

* *T*: 入力テーブル式。
* *Idcolumn*: ユーザーアクティビティを表す ID 値を持つ列の名前。 
* *TimelineColumn*: タイムラインを表す列の名前。
* *Start*: 分析の開始期間の値を使用したスカラー。
* *End*: 分析終了期間の値を使用したスカラー。
* ルックバック*ウィンドウ*: 前方の期間のスカラー定数値 (たとえば、 `dcount` 過去 7D: look backwindow = 7d のユーザーの場合)
* *Bin*: 分析ステップ期間のスカラー定数値。 この値には、数値/日付/時刻/タイムスタンプ値を指定できます。 値が形式の文字列の場合 `week` / `month` / `year` 、すべての期間は[startofweek](startofweekfunction.md) / [startofmonth](startofmonthfunction.md) / [startofyear](startofyearfunction.md)になります。 
* *dim1*、 *dim2*、...: (省略可能) アクティビティメトリックスの計算をスライスするディメンション列の一覧です。

## <a name="returns"></a>戻り値

各タイムライン期間 (ビン単位)、および既存のディメンションの組み合わせごとに、Id のカウントと個別のカウントの値を持つテーブルを返します。

出力テーブルスキーマは次のとおりです。

|*TimelineColumn*|`dim1`|..|`dim_n`|`count`|`dcount`|
|---|---|---|---|---|---|
|型: as of *TimelineColumn*|..|..|..|long|long|


## <a name="examples"></a>例

`dcounts`分析期間の各日について、過去1週間のカウントとユーザーの数を計算します。 

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

|Timestamp|Count|`dcount`|
|---|---|---|
|2017-08-01 00:00: 00.0000000|5|3|
|2017-08-02 00:00: 00.0000000|8|5|
|2017-08-03 00:00: 00.0000000|13|5|
|2017-08-04 00:00: 00.0000000|9|5|
|2017-08-05 00:00: 00.0000000|7|5|
|2017-08-06 00:00: 00.0000000|2|2|
|2017-08-07 00:00: 00.0000000|1|1|