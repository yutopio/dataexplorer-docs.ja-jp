---
title: sequence_detect プラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの sequence_detect プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e8da8a61b285b31f63f346ec82e5ba8a4ac00d27
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372928"
---
# <a name="sequence_detect-plugin"></a>sequence_detect プラグイン

指定された述語に基づいてシーケンスの発生を検出します。

```kusto
T | evaluate sequence_detect(datetime_column, 10m, 1h, e1 = (Col1 == 'Val'), e2 = (Col2 == 'Val2'), Dim1, Dim2)
```

**構文**

*T* `| evaluate` `sequence_detect` `(` *TimelineColumn* `,` *maxsequencestepwindow* `,` *MaxSequenceSpan* `,` *expr1 or* `,` *Expr2* `,` ..., *Dim1* `,` *Dim2* `,` ...`)`

**引数**

* *T*: 入力テーブル式。
* *TimelineColumn*: タイムラインを表す列参照は、ソース式に存在する必要があります
* *Maxsequencestepwindow*: シーケンス内の2つの連続するステップ間で許容される最大の timespan のスカラー定数値
* *MaxSequenceSpan*: シーケンスがすべてのステップを完了するための最大スパンのスカラー定数値
* *Expr1 or*, *Expr2*,...: シーケンスステップを定義するブール述語式
* *Dim1*, *Dim2*,...: シーケンスを関連付けるために使用されるディメンション式

**戻り値**

1つのテーブルを返します。テーブルの各行は1つのシーケンスの出現を表します。

* *Dim1*, *Dim2*,...: シーケンスを関連付けるために使用されたディメンション列。
* *Expr1 or*_*TimelineColumn*、 *Expr2*_*TimelineColumn*、...: time 値を持つ列。各シーケンス手順のタイムラインを表します。
* *Duration*: シーケンスの全体的な時間枠

**使用例**

### <a name="exploring-storm-events"></a>ストームイベントの調査 

次のクエリでは、テーブル StormEvents (2007 の weather 統計) を確認し、5日以内に ' 過剰な熱 ' のシーケンスに ' なり ' が続くケースを示します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| evaluate sequence_detect(
        StartTime,
        5d,  // step max-time
        5d,  // sequence max-time
        heat=(EventType == "Excessive Heat"), 
        wildfire=(EventType == 'Wildfire'), 
        State)
```

|State|heat_StartTime|wildfire_StartTime|Duration|
|---|---|---|---|
|カリフォルニア|2007-05-08 00:00: 00.0000000|2007-05-08 16:02: 00.0000000|16:02:00|
|カリフォルニア|2007-05-08 00:00: 00.0000000|2007-05-10 11:30: 00.0000000|2.11:30:00|
|カリフォルニア|2007-07-04 09:00: 00.0000000|2007-07-05 23:01: 00.0000000|1.14:01:00|
|サウスダコタ|2007-07-23 12:00: 00.0000000|2007-07-27 09:00: 00.0000000|3.21:00:00|
|テキサス州|2007-08-10 08:00: 00.0000000|2007-08-11 13:56: 00.0000000|1.05:56:00|
|カリフォルニア|2007-08-31 08:00: 00.0000000|2007-09-01 11:28: 00.0000000|1.03:28:00|
|カリフォルニア|2007-08-31 08:00: 00.0000000|2007-09-02 13:30: 00.0000000|2.05:30:00|
|カリフォルニア|2007-09-02 12:00: 00.0000000|2007-09-02 13:30: 00.0000000|01:30:00|
