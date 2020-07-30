---
title: funnel_sequence_completion プラグイン-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの funnel_sequence_completion プラグインについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/16/2020
ms.openlocfilehash: 3511d15ebf0f5e3708deeeed981a8a6808da2e48
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347937"
---
# <a name="funnel_sequence_completion-plugin"></a>funnel_sequence_completion プラグイン

さまざまな期間を比較して、完了したシーケンスステップのじょうごを計算します。

```kusto
T | evaluate funnel_sequence_completion(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, state_column, dynamic(['S1', 'S2', 'S3']), dynamic([10m, 30min, 1h]))
```

## <a name="syntax"></a>構文

*T* `| evaluate` `funnel_sequence_completion(` *idcolumn* `,` *TimelineColumn* `,` *Start* `,` *End* `,` *Step* `,` *statecolumn* `,` *シーケンス* `,` *maxsequencestepwindows*`)`

## <a name="arguments"></a>引数

* *T*: 入力テーブル式。
* *Idcolum*: 列参照。ソース式に存在する必要があります。
* *TimelineColumn*: タイムラインを表す列参照は、ソース式に存在する必要があります。
* *Start*: 分析の開始期間のスカラー定数値。
* *End*: 分析終了期間のスカラー定数値。
* *ステップ*: 分析ステップ期間 (bin) のスカラー定数値。
* *Statecolumn*: 状態を表す列参照は、ソース式に存在する必要があります。
* *Sequence*: シーケンス値を持つ定数動的配列 (値はで検索され `StateColumn` ます)。
* *Maxsequencestepwindows*: シーケンス内の最初と最後の連続するステップ間の最大許容期間の値を持つスカラー定数動的配列。 配列の各ウィンドウ (ピリオド) では、じょうご分析の結果が生成されます。

## <a name="returns"></a>戻り値

分析されたシーケンスのじょうごグラフを作成するのに役立つ単一のテーブルを返します。

* `TimelineColumn`: 分析された時間枠
* `StateColumn`: シーケンスの状態。
* `Period`: シーケンスの最初のステップから測定されたじょうごシーケンスのステップを完了するために使用できる最大期間 (ウィンドウ)。 *Maxsequencestepwindows*の各値によって、個別のピリオドでじょうご分析が生成されます。 
* `dcount`: `IdColumn` 最初のシーケンス状態からの値に遷移した時間枠の個別のカウント `StateColumn` 。

## <a name="examples"></a>例

### <a name="exploring-storm-events"></a>ストームイベントの調査 

次のクエリでは、シーケンスの完了じょうごをチェックします。 `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` "全体" の時間は1時間、timegenerated>now-4hours、1日です。 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let _start = datetime(2007-01-01);
let _end =  datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods) 
```

|`StartTime`|`EventType`|`Period`|`dcount`|
|---|---|---|---|
|2007-01-01 00:00: 00.0000000|ひょう|01:00:00|2877|
|2007-01-01 00:00: 00.0000000|Tornado|01:00:00|208|
|2007-01-01 00:00: 00.0000000|雷雨風|01:00:00|87|
|2007-01-01 00:00: 00.0000000|ひょう|04:00:00|2877|
|2007-01-01 00:00: 00.0000000|Tornado|04:00:00|231|
|2007-01-01 00:00: 00.0000000|雷雨風|04:00:00|141|
|2007-01-01 00:00: 00.0000000|ひょう|1.00:00:00|2877|
|2007-01-01 00:00: 00.0000000|Tornado|1.00:00:00|244|
|2007-01-01 00:00: 00.0000000|雷雨風|1.00:00:00|155|

結果を理解します。  
結果は3つのじょうご (期間: 1 時間、4時間、1日) です。 じょうごの各ステップについて、多数の個別のカウントが表示されます。 のシーケンス全体を完了するためにより多くの時間が与えられていることがわかり `Hail`  ->  `Tornado`  ->  `Thunderstorm Wind` `dcount` ます。高い値が取得されます。 つまり、シーケンスがじょうごステップに到達する回数が増えていました。
