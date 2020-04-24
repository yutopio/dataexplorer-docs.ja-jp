---
title: funnel_sequence_completionプラグイン - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーfunnel_sequence_completionプラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/16/2020
ms.openlocfilehash: 7f168841db2df47e4e3a192b75585a1fe4d83718
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514775"
---
# <a name="funnel_sequence_completion-plugin"></a>funnel_sequence_completion プラグイン

異なる期間を比較する中で、完了したシーケンスステップの漏斗を計算します。

```kusto
T | evaluate funnel_sequence_completion(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, state_column, dynamic(['S1', 'S2', 'S3']), dynamic([10m, 30min, 1h]))
```

**構文**

*T* `| evaluate` `,` *Step* *IdColumn* `,` `,` *Start* `,` `,` `,` *Sequence* *End* *TimelineColumn* `,` *StateColumn* IdColumn タイムライン列開始ステップ状態列シーケンス*MaxSequenceStepWindows* `funnel_sequence_completion(``)`

**引数**

* *T*: 入力表形式の式。
* *IdColum*: 列参照は、ソース式に存在する必要があります。
* *タイムライン列*: タイムラインを表す列参照は、ソース式に存在する必要があります。
* *開始*: 分析開始期間のスカラー定数値
* *終了*: 分析終了期間のスカラー定数値
* *ステップ*: 分析ステップ周期のスカラー定数値 (bin) 
* *状態列*: 状態を表す列参照は、ソース式に存在する必要があります。
* *シーケンス*: シーケンス値を持つ定数動的配列 (値は`StateColumn`で検索されます)
* *MaxSequenceStepWindows*: シーケンスの最初と最後の連続したステップの間の最大許容タイムスパンの値を持つスカラー定数動的配列は、配列内の各ウィンドウ (期間) がファネル分析結果を生成します。

**戻り値**

分析されたシーケンスのじょうご図を作成するのに役立つ単一のテーブルを返します。

* タイムライン列: 分析された時間枠
* `StateColumn`: シーケンスの状態。
* 期間: シーケンスの最初のステップから測定されたじょうごシーケンスのステップを完了するために許容される最大期間(ウィンドウ)。 *MaxSequenceStepWindows*の各値は、個別の期間を持つファネル分析を生成します。 
* dcount: 最初の`IdColumn`シーケンス状態から の値に遷移したタイム ウィンドウ内`StateColumn`の個別のカウント。

**使用例**

### <a name="exploring-storm-events"></a>嵐のイベントを探る 

次のクエリは、シーケンスの完了漏斗を`Hail` -> `Tornado` -> `Thunderstorm Wind`チェックします: "全体" 時間 1 時間、4 時間、1 日。 

```kusto
let _start = datetime(2007-01-01);
let _end =  datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods) 
```

|StartTime|EventType|期間|dcount|
|---|---|---|---|
|2007-01-01 00:00:00.0000000|ひょう|01:00:00|2877|
|2007-01-01 00:00:00.0000000|竜巻|01:00:00|208|
|2007-01-01 00:00:00.0000000|雷雨風|01:00:00|87|
|2007-01-01 00:00:00.0000000|ひょう|04:00:00|2877|
|2007-01-01 00:00:00.0000000|竜巻|04:00:00|231|
|2007-01-01 00:00:00.0000000|雷雨風|04:00:00|141|
|2007-01-01 00:00:00.0000000|ひょう|1.00:00:00|2877|
|2007-01-01 00:00:00.0000000|竜巻|1.00:00:00|244|
|2007-01-01 00:00:00.0000000|雷雨風|1.00:00:00|155|

結果を理解する:  
結果は3つの漏斗(期間:1時間、4時間、1日)、各漏斗ステップごとにエピソードIdの数が示されています。 より大きな値のシーケンス全体を完了する時間が多`Hail` -> `Tornado` -> `Thunderstorm Wind`くなる`dcount`ことがわかります(漏斗のステップに到達するシーケンスの発生が多いことを意味します)。
