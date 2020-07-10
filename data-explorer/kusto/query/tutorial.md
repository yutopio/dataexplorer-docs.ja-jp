---
title: チュートリアル-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのチュートリアルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 2060d2996338cf1eee33b5905e9929c46040afa9
ms.sourcegitcommit: b286703209f1b657ac3d81b01686940f58e5e145
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86188593"
---
# <a name="tutorial"></a>チュートリアル

::: zone pivot="azuredataexplorer"

Kusto クエリ言語について学習する最善の方法は、いくつかの単純なクエリを見て、[いくつかのサンプルデータを含むデータベース](https://help.kusto.windows.net/Samples)を使用して言語の "感覚" を取得することです。 この記事に示されているクエリは、そのデータベースで実行する必要があります。 `StormEvents`このサンプルデータベースの表では、米国で発生したストームに関する情報を提供しています。

<!--
  TODO: Provide link to reference data we used originally in StormEvents
-->

<!--
  TODO: A few samples below reference non-existent tables, such as Events and Logs.
        We need to add these tables.
-->

## <a name="count-rows"></a>行数のカウント

このサンプルデータベースには、という名前のテーブルがあり `StormEvents` ます。
その大きさを調べるために、次のように単に行数をカウントする演算子にコンテンツをパイプします。

* *構文:* クエリは、データソース (通常はテーブル名) で、必要に応じてパイプ文字の1つ以上のペアと、表形式演算子を指定します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

結果は次のようになります。

|Count|
|-----|
|59066|
    
[count 演算子](./countoperator.md)。

## <a name="project-select-a-subset-of-columns"></a>プロジェクト: 列のサブセットを選択します

必要な列だけを取得するには、 [project](./projectoperator.md)を使用します。 [Project](./projectoperator.md)と[take](./takeoperator.md)演算子の両方を使用する次の例を参照してください。

## <a name="where-filtering-by-a-boolean-expression"></a>where: ブール式によるフィルター処理

では、 `flood` `California` 2007 年2月にのみを見てみましょう。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

|StartTime|EndTime|状態|EventType|EpisodeNarrative|
|---|---|---|---|---|
|2007-02-19 00:00: 00.0000000|2007-02-19 08:00: 00.0000000|カリフォルニア|洪水|南のサンホアキンバレーで正面システムを移動すると、午前19時の午前1時に、雨の雨を西洋のに簡単に移行できます。 軽微なフラッディングは、Taft 付近の州幹線道路166で報告されています。|

## <a name="take-show-me-n-rows"></a>take: n 行を表示する

たとえば、以下のように 5 個の行を表示するとします。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|EventType|状態|EventNarrative|
|---|---|---|---|---|
|2007-09-18 20:00: 00.0000000|2007-09-19 18:00: 00.0000000|重い雨|フロリダ|Coastal Volusia 郡の一部にわたって24時間のうち9インチの雨がいます。|
|2007-09-20 21:57: 00.0000000|2007-09-20 22:05: 00.0000000|Tornado|フロリダ|Eustis 町で触れた tornado は、西 Crooked Lake の北端にあります。 Tornado は、北北西を Eustis 移動したときに EF1 の強さをすばやく極めるします。 このトラックは、長さが2マイル未満で、最大幅が300ヤードになっています。  Tornado は7本の自宅を破壊しています。 20個の自宅が、大きなダメージを受け、81の自宅が軽微な損害を報告しました。 重大な負傷や、プロパティの破損は $620万に設定されていました。|
|2007-09-29 08:11: 00.0000000|2007-09-29 08:11: 00.0000000|Waterspout|大西洋南部|メルボルンの東南南東部に形成され、waterspout が簡単に海岸に移行しました。|
|2007-12-20 07:50: 00.0000000|2007-12-20 07:53: 00.0000000|雷雨風|MISSISSIPPI|多数の大規模なツリーが、いくつかダウンしています。 東 Adams 郡で破損が発生しました。|
|2007-12-30 16:00: 00.0000000|2007-12-30 16:05: 00.0000000|雷雨風|グルジア|郡のディスパッチでは、複数のツリーが、州道路206付近の Quincey Batten ループに沿って表示されました。 ツリーの削除コストが推定されました。|

ただし、テーブルの[行は特定](./takeoperator.md)の順序で表示されないので、並べ替えてみましょう。
* [limit](./takeoperator.md)は[take](./takeoperator.md)のエイリアスであるため、同じ効果が得られます。

## <a name="sort-and-top"></a>sort と top

* *構文:* 一部の演算子には、などのキーワードによって導入されたパラメーターがあり `by` ます。
* `desc` は降順、`asc` は昇順を意味します。

特定の列で並べ替えた最初の n 個の行を表示する場合は、以下のように指定します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|EventType|状態|EventNarrative|
|---|---|---|---|---|
|2007-12-31 22:30: 00.0000000|2007-12-31 23:59: 00.0000000|冬の嵐|ミシガン|この大きな雪のイベントは、新年の朝に続きます。|
|2007-12-31 22:30: 00.0000000|2007-12-31 23:59: 00.0000000|冬の嵐|ミシガン|この大きな雪のイベントは、新年の朝に続きます。|
|2007-12-31 22:30: 00.0000000|2007-12-31 23:59: 00.0000000|冬の嵐|ミシガン|この大きな雪のイベントは、新年の朝に続きます。|
|2007-12-31 23:53: 00.0000000|2007-12-31 23:53: 00.0000000|高風|カリフォルニア|北から北東への風 gusting と約 58 mph が Ventura 郡の山で報告されました。|
|2007-12-31 23:53: 00.0000000|2007-12-31 23:53: 00.0000000|高風|カリフォルニア|ウォームスプリング RAWS センサーは、northerly 風 gusting を 58 mph に報告しました。|

[Sort](./sortoperator.md)を使用して、 [take](./takeoperator.md)演算子を使用すると、同じことができます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndLat, EventType, EventNarrative
```

## <a name="extend-compute-derived-columns"></a>拡張: 計算派生列

すべての行の値を計算して、新しい列を作成します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

|StartTime|EndTime|期間|EventType|状態|
|---|---|---|---|---|
|2007-09-18 20:00: 00.0000000|2007-09-19 18:00: 00.0000000|22:00:00|重い雨|フロリダ|
|2007-09-20 21:57: 00.0000000|2007-09-20 22:05: 00.0000000|00:08:00|Tornado|フロリダ|
|2007-09-29 08:11: 00.0000000|2007-09-29 08:11: 00.0000000|00:00:00|Waterspout|大西洋南部|
|2007-12-20 07:50: 00.0000000|2007-12-20 07:53: 00.0000000|00:03:00|雷雨風|MISSISSIPPI|
|2007-12-30 16:00: 00.0000000|2007-12-30 16:05: 00.0000000|00:05:00|雷雨風|グルジア|

列名を再利用し、計算結果を同じ列に割り当てることができます。
次に例を示します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print x=1
| extend x = x + 1, y = x
| extend x = x + 1
```

|x|Y|
|---|---|
|3|1|

[スカラー式](./scalar-data-types/index.md)には、通常の演算子 ( `+` 、、、、) をすべて含めることができ `-` `*` `/` `%` ます。また、さまざまな便利な関数があります。

## <a name="summarize-aggregate-groups-of-rows"></a>まとめ: 行の集計グループ

各国から取得したイベントの数をカウントします。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count = count() by State
```

句内の同じ値を持つ行[をグループ](./summarizeoperator.md)化 `by` し、集計関数 (など) を使用して `count` 各グループを1行にまとめます。 そのため、この場合は、状態ごとに1行、その状態の行の数を示す列があります。

[集計関数](./summarizeoperator.md#list-of-aggregation-functions)がいくつかあります。1つの集計演算子で使用して、複数の計算列を生成することができます。 たとえば、各州のストームの数を取得し、状態ごとの嵐の種類を合計することもできます。  
次に、 [top](./topoperator.md)を使用して、最もストームに影響する状態を取得できます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

|状態|StormCount|TypeOfStorms|
|---|---|---|
|テキサス州|4701|27|
|カンザス|3166|21|
|アイオワ州|2337|19|
|イリノイ州|2022|23|
|州|2016|20|

summarize の結果には以下のものが含まれます。

* `by`で指定された各列
* 各計算式の列。
* `by` 値の組み合わせごとに 1 つの行

## <a name="summarize-by-scalar-values"></a>スカラー値による集計

句ではスカラー (数値、時刻、または間隔) の値を使用でき `by` ますが、値をビンに入れることをお勧めします。  
[Bin ()](./binfunction.md)関数は、次の場合に役立ちます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

これにより、すべてのタイムスタンプが1日の間隔に短縮されます。

|StartTime|event_count|
|---|---|
|2007-02-14 00:00: 00.0000000|180|
|2007-02-15 00:00: 00.0000000|66|
|2007-02-16 00:00: 00.0000000|164|
|2007-02-17 00:00: 00.0000000|103|
|2007-02-18 00:00: 00.0000000|22|
|2007-02-19 00:00: 00.0000000|52|
|2007-02-20 00:00: 00.0000000|60|

[Bin ()](./binfunction.md)は、多くの言語の[floor ()](./floorfunction.md)関数と同じです。 これにより、すべての値が、指定した剰余の倍数になるように単純化されるので、[集計](./summarizeoperator.md)によって行をグループに割り当てることができます。

## <a name="render-display-a-chart-or-table"></a>Render: グラフまたはテーブルを表示します。

2つの列を射影し、グラフの x 軸と y 軸として使用します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tutorial/event-counts-state.png" alt-text="状態別のストームイベント数の縦棒グラフ":::

プロジェクトの操作では削除しましたが `mid` 、グラフでその注文の国を表示する必要がある場合は、引き続き必要です。

厳密に言うと、' render ' はクエリ言語の一部ではなく、クライアントの機能です。 それでも、言語に統合されており、結果を構想するうえで非常に便利です。


## <a name="timecharts"></a>時間グラフ

数値ビンに戻ると、タイムシリーズが表示されるようになります。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tutorial/time-series-start-bin.png" alt-text="時間別の折れ線グラフイベントビン分割":::

## <a name="multiple-series"></a>複数の系列

値の組み合わせごとに別の行を作成する場合は、以下のように、 `summarize by` 句で複数の値を使用します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tutorial/table-count-source.png" alt-text="ソース別のテーブル数":::

上記のにレンダリング語句を追加するだけ `| render timechart` です。

:::image type="content" source="images/tutorial/line-count-source.png" alt-text="ソース別の折れ線グラフ数":::

では `render timechart` 最初の列が x 軸として使用され、他の列は個別の行として表示されることに注意してください。

## <a name="daily-average-cycle"></a>日次平均サイクル

活動は平均1日にどのように変化しますか。

1日の時間単位でイベントをカウントします。 Bin の代わりにを使用することに注意して `floor` ください。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tutorial/time-count-hour.png" alt-text="時間別の時間グラフの数":::

現在、では、 `render` 期間に適切にラベル付けされませんが、代わりにを使用でき `| render columnchart` ます。

:::image type="content" source="images/tutorial/column-count-hour.png" alt-text="1時間ごとの縦棒グラフ":::

## <a name="compare-multiple-daily-series"></a>複数の日次系列の比較

さまざまな状態の時間帯におけるアクティビティの違い

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

:::image type="content" source="images/tutorial/time-hour-state.png" alt-text="時間と状態別の時間グラフ":::

X 軸を継続時間ではなく時間番号にするには、を除算し `1h` ます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tutorial/column-hour-state.png" alt-text="時間と州別の縦棒グラフ":::

## <a name="join"></a>Join

2つの EventTypes がどちらの状態で発生したかを調べるにはどうすればよいでしょうか。

最初の EventType と2つ目の EventType を使用して、嵐イベントを取得し、2つのセットを状態に結合できます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where EventType == "Lightning"
| join (
    StormEvents 
    | where EventType == "Avalanche"
) on State  
| distinct State
```

:::image type="content" source="images/tutorial/join-events-la.png" alt-text="イベントの結合 (稲妻と大量)":::

## <a name="user-session-example-of-join"></a>ユーザーセッションの結合の例

このセクションでは、テーブルは使用しません `StormEvents` 。

各ユーザーセッションの開始と終了を示すイベントと、各セッションの一意の ID を含むデータがあると仮定します。 

各ユーザーセッションが最後にどれくらいの時間になりますか。

を使用して `extend` 2 つのタイムスタンプのエイリアスを指定することで、セッション継続時間を計算できます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
Events
| where eventName == "session_started"
| project start_time = timestamp, stop_time, country, session_id
| join ( Events
    | where eventName == "session_ended"
    | project stop_time = timestamp, session_id
    ) on session_id
| extend duration = stop_time - start_time
| project start_time, stop_time, country, duration
| take 10
```

:::image type="content" source="images/tutorial/user-session-extend.png" alt-text="ユーザーセッションの拡張":::

結合を実行する前に必要な列のみを選択する場合は、 `project` を使用することをお勧めします。
その場合、同じ句で、タイムスタンプ列の名前を変更します。

## <a name="plot-a-distribution"></a>分布のプロット

長さが異なるのは、どのくらいのストームがあるのでしょうか。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend  duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count()
    by bin(duration, 5m)
| sort by duration asc
| render timechart
```

:::image type="content" source="images/tutorial/event-count-duration.png" alt-text="期間別のイベント数線上":::

またはを使用し `| render columnchart` ます。

:::image type="content" source="images/tutorial/column-event-count-duration.png" alt-text="期間別の縦棒グラフイベント数線上":::

## <a name="percentiles"></a>パーセンタイル

期間の範囲は、ストームの割合によって異なりますか。

上記のクエリを使用しますが、 `render` をに置き換えます。

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

この場合、句を指定しなかったので、 `by` 結果は単一行になります。

:::image type="content" source="images/tutorial/summarize-percentiles-duration.png" alt-text="期間別のパーセンタイルの概要テーブル":::

この結果から次のことがわかります。

* 嵐の5% は5分未満の期間です。
* 25m より前の嵐の50%
* 50m. の5% 以上のストーム

状態ごとに個別の内訳を取得するには、両方の集計演算子を使用して state 列を個別に取得する必要があります。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend  duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count()
    by bin(duration, 5m), State
| sort by duration asc
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

:::image type="content" source="images/tutorial/summarize-percentiles-state.png" alt-text="状態別のパーセンタイル期間の概要表":::

## <a name="let-assign-a-result-to-a-variable"></a>let: 結果を変数に代入する

上記の "結合" の例でクエリ式の各部分を分離するには、 [let](./letstatement.md)を使用します。 結果は変わりません。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let LightningStorms = 
    StormEvents
    | where EventType == "Lightning";
let AvalancheStorms = 
    StormEvents
    | where EventType == "Avalanche";
LightningStorms 
| join (AvalancheStorms) on State
| distinct State
```

> [!TIP]
> Kusto エクスプローラークライアントでは、このの部分の間に空白行を入れないでください。 必ず、すべて間を空けずに実行してください。

## <a name="combining-data-from-several-databases-in-a-query"></a>クエリ内の複数のデータベースからのデータの結合

詳細については、「[複数データベースにまたがるクエリ](./cross-cluster-or-database-queries.md)」を参照してください。

スタイルのクエリを記述すると、次のようになります。

```kusto
Logs | where ...
```

Logs という名前のテーブルは、既定のデータベースに存在している必要があります。 別のデータベースからテーブルにアクセスする場合は、次の構文を使用します。

```kusto
database("db").Table
```

そのため、*診断*と*テレメトリ*という名前のデータベースがあり、そのデータの一部を関連付ける必要がある場合は、(既定のデータベースとして*診断*が想定されている) を記述することもできます。

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

既定のデータベースが*テレメトリ*の場合

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
上記では、両方のデータベースが現在接続しているクラスターに存在していることを前提としています。 *テレメトリ*データベースが*TelemetryCluster.kusto.windows.net*という別のクラスターに属していて、それにアクセスするために必要なものとします。

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> [!NOTE]
> クラスターが指定されている場合、データベースは必須です

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
