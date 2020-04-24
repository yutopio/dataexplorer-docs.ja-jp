---
title: sequence_detectプラグイン - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーsequence_detectプラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: afe4b41cc9bf3e81389edfb1fac76472772fa8f6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509182"
---
# <a name="sequence_detect-plugin"></a>sequence_detectプラグイン

指定された述部に基づいてシーケンスオカレンスを検出します。

```kusto
T | evaluate sequence_detect(datetime_column, 10m, 1h, e1 = (Col1 == 'Val'), e2 = (Col2 == 'Val2'), Dim1, Dim2)
```

**構文**

*T*`| evaluate``,``,``,``,``,`*TimelineColumn*`,`*Dim1**Dim2**MaxSequenceSpan**Expr1**MaxSequenceStepWindow**Expr2*タイムラインコラム`,`MaxSequenceStepWindow マックスシーケンススパン エクスプロ1 エクスプル2., Dim1 Dim2 .. `sequence_detect` `(``)`

**引数**

* *T*: 入力表形式の式。
* *タイムライン列*: タイムラインを表す列参照は、ソース式に存在する必要があります。
* *MaxSequenceStepWindow*: シーケンス内の 2 つの連続したステップ間の最大許容タイムスパンのスカラー定数値
* *MaxSequenceSpan*: シーケンスがすべてのステップを完了するための最大スパンのスカラー定数値
* *Expr1* *、Expr2*、..: シーケンスステップを定義するブール述語式
* *Dim1*, *Dim2*, .. シーケンスを相関させるために使用される次元式

**戻り値**

テーブル内の各行が 1 つのシーケンスを表す単一のテーブルを返します。

* *Dim1*, *Dim2*,..: シーケンスを相関させるために使用された次元列。
* *Expr1*_*タイムライン列* *、Expr2*_*タイムライン列*、..: 各シーケンス ステップのタイムラインを表す時間値を持つ列。
* *所要時間*: シーケンスの全ウィンドウ

**使用例**

### <a name="exploring-storm-events"></a>嵐のイベントを探る 

次のクエリは、StormEvents (2007 年の気象統計) テーブルを調べ、5 日以内に '過剰熱' のシーケンスの後に 「山火事」が続いたケースを示します。

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
|カリフォルニア|2007-05-08 00:00:00.0000000|2007-05-08 16:02:00.0000000|16:02:00|
|カリフォルニア|2007-05-08 00:00:00.0000000|2007-05-10 11:30:00.0000000|2.11:30:00|
|カリフォルニア|2007-07-04 09:00:00.0000000|2007-07-05 23:01:00.0000000|1.14:01:00|
|サウスダコタ|2007-07-23 12:00:00.0000000|2007-07-27 09:00:00.0000000|3.21:00:00|
|テキサス州|2007-08-10 08:00:00.0000000|2007-08-11 13:56:00.0000000|1.05:56:00|
|カリフォルニア|2007-08-31 08:00:00.0000000|2007-09-01 11:28:00.0000000|1.03:28:00|
|カリフォルニア|2007-08-31 08:00:00.0000000|2007-09-02 13:30:00.0000000|2.05:30:00|
|カリフォルニア|2007-09-02 12:00:00.0000000|2007-09-02 13:30:00.0000000|01:30:00|