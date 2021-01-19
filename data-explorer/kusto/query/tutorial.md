---
title: チュートリアル:Azure Data Explorer と Azure Monitor での Kusto クエリ
description: このチュートリアルでは、Kusto クエリ言語でクエリを使用して、Azure Data Explorer と Azure Monitor での一般的なクエリのニーズを満たす方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/08/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 930936bd9839730ccb0c438cc96a67334ea7ac71
ms.sourcegitcommit: 64b7b320875950dfee8eb1a23d36aa95e27d7297
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2021
ms.locfileid: "98207807"
---
# <a name="tutorial-use-kusto-queries-in-azure-data-explorer-and-azure-monitor"></a>チュートリアル:Azure Data Explorer と Azure Monitor で Kusto クエリを使用する

::: zone pivot="azuredataexplorer"

Kusto クエリ言語について学習する最善の方法は、いくつかの基本的なクエリを見て、言語の "感覚" をつかむことです。 [いくつかのサンプル データが含まれるデータベース](https://help.kusto.windows.net/Samples)を使用することをお勧めします。 このチュートリアルで示すクエリは、そのデータベースで実行する必要があります。 サンプル データベースの `StormEvents` テーブルでは、米国で発生した嵐に関する情報が提供されています。

## <a name="count-rows"></a>行数のカウント

このサンプル データベースには、`StormEvents` という名前のテーブルがあります。 テーブルの大きさを調べるため、テーブル内の行数を単純にカウントする演算子にその内容をパイプします。 

*構文に関する注意*: クエリは、データ ソース (通常はテーブル名) の後に、必要に応じてパイプ文字の 1 つ以上のペアと、表形式演算子を指定したものです。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

出力は次のようになります。

|Count|
|-----|
|59066|
    
詳細については、「[count 演算子](./countoperator.md)」を参照してください。

## <a name="select-a-subset-of-columns-project"></a>列のサブセットを選択する: *project*

必要な列だけを取得するには、[project](./projectoperator.md) を使用します。 次の例を参照してください。この例では、[project](./projectoperator.md) と [take](./takeoperator.md) 演算子が使用されています。

## <a name="filter-by-boolean-expression-where"></a>ブール式でフィルター処理を行う: *where*

2007 年 2 月の `California` における `flood` イベントのみを表示しましょう。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

出力は次のようになります。

|StartTime|EndTime|状態|EventType|EpisodeNarrative|
|---|---|---|---|---|
|2007-02-19 00:00:00.0000000|2007-02-19 08:00:00.0000000|CALIFORNIA|洪水|A frontal system moving across the Southern San Joaquin Valley brought brief periods of heavy rain to western Kern County in the early morning hours of the 19th. Minor flooding was reported across State Highway 166 near Taft.|

## <a name="show-n-rows-take"></a>*n* 行を表示する: *take*

データをいくつか見てみましょう。 5 行のランダムなサンプルはどのような内容でしょうか。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

出力は次のようになります。

|StartTime|EndTime|EventType|状態|EventNarrative|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|Heavy Rain|FLORIDA|As much as 9 inches of rain fell in a 24-hour period across parts of coastal Volusia County.|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|Tornado|FLORIDA|A tornado touched down in the Town of Eustis at the northern end of West Crooked Lake. The tornado quickly intensified to EF1 strength as it moved north northwest through Eustis. The track was just under two miles long and had a maximum width of 300 yards.  The tornado destroyed 7 homes. Twenty seven homes received major damage and 81 homes reported minor damage. There were no serious injuries and property damage was set at $6.2 million.|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|Waterspout|ATLANTIC SOUTH|A waterspout formed in the Atlantic southeast of Melbourne Beach and briefly moved toward shore.|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|雷雨風|MISSISSIPPI|Numerous large trees were blown down with some down on power lines. Damage occurred in eastern Adams county.|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|雷雨風|GEORGIA|The county dispatch reported several trees were blown down along Quincey Batten Loop near State Road 206. The cost of tree removal was estimated.|

ただし、[take](./takeoperator.md) を使用して表示されるテーブルの行は特定の順序になっていないので、並べ替えてみましょう。 ([limit](./takeoperator.md) は [take](./takeoperator.md) の別名であり、効果は同じです)。

## <a name="order-results-sort-top"></a>結果の順序を指定する: *sort*、*top*

* *構文に関する注意*: 一部の演算子には、`by` のようなキーワードで指定するパラメーターがあります。
* 次の例では、`desc` を指定すると結果が降順に並べ替えられ、`asc` を指定すると結果が昇順に並べ替えられます。

特定の列で並べ替えた最初の *n* 行を表示します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

出力は次のようになります。

|StartTime|EndTime|EventType|状態|EventNarrative|
|---|---|---|---|---|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Winter Storm|ミシガン|This heavy snow event continued into the early morning hours on New Year's Day.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Winter Storm|ミシガン|This heavy snow event continued into the early morning hours on New Year's Day.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Winter Storm|ミシガン|This heavy snow event continued into the early morning hours on New Year's Day.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|High Wind|CALIFORNIA|North to northeast winds gusting to around 58 mph were reported in the mountains of Ventura county.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|High Wind|CALIFORNIA|The Warm Springs RAWS sensor reported northerly winds gusting to 58 mph.|

[sort](./sortoperator.md) を使用してから [take](./takeoperator.md) を使用することにより、同じ結果を得ることができます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndTime, EventType, EventNarrative
```

## <a name="compute-derived-columns-extend"></a>派生列を計算する: *extend*

すべての行の値を計算することにより、新しい列を作成します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

出力は次のようになります。

|StartTime|EndTime|Duration|EventType|状態|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|22:00:00|Heavy Rain|FLORIDA|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|00:08:00|Tornado|FLORIDA|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|00:00:00|Waterspout|ATLANTIC SOUTH|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|00:03:00|雷雨風|MISSISSIPPI|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|00:05:00|雷雨風|GEORGIA|

列の名前を再利用し、計算結果を同じ列に割り当てることができます。

例:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print x=1
| extend x = x + 1, y = x
| extend x = x + 1
```

出力は次のようになります。

|x|y|
|---|---|
|3|1|

[スカラー式](./scalar-data-types/index.md)には、すべての通常の演算子 (`+`、`-`、`*`、`/`、`%`) を含めることができ、さまざまな役に立つ関数を使用できます。

## <a name="aggregate-groups-of-rows-summarize"></a>行のグループを集計する: *summarize*

各州で発生したイベントの数をカウントします。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count = count() by State
```

[summarize](./summarizeoperator.md) を使用して `by` 句の値が同じ行をグループ化した後、集計関数 (`count` など) を使用して各グループを 1 つの行に組み合わせます。 この例では、州ごとに 1 つの行があり、その州の行数用の列が 1 つあります。

さまざまな[集計関数](./summarizeoperator.md#list-of-aggregation-functions)を使用できます。 1 つの `summarize` 演算子で複数の集計関数を使用して、複数の計算列を生成できます。 たとえば、各州の嵐の数や、州ごとの嵐の種類別の合計数を取得することができます。 次に、[top](./topoperator.md) を使用して、嵐の影響を最も受ける州を取得できます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

出力は次のようになります。

|状態|StormCount|TypeOfStorms|
|---|---|---|
|テキサス州|4701|27|
|KANSAS|3166|21|
|アイオワ州|2337|19|
|ILLINOIS|2022|23|
|MISSOURI|2016|20|

`summarize` 演算子の結果では、次のようになります。

* 各列の名前は `by` で指定します。
* 計算式ごとに 1 つの列があります。
* `by` の値の組み合わせごとに 1 つの行があります。

## <a name="summarize-by-scalar-values"></a>スカラー値による集計

`by` 句ではスカラー (数値、時刻、または間隔) の値を使用できますが、[bin()](./binfunction.md) 関数を使用して値をビンに入れることをお勧めします。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

このクエリにより、すべてのタイムスタンプが 1 日の間隔の数に削減されます。

|StartTime|event_count|
|---|---|
|2007-02-14 00:00:00.0000000|180|
|2007-02-15 00:00:00.0000000|66|
|2007-02-16 00:00:00.0000000|164|
|2007-02-17 00:00:00.0000000|103|
|2007-02-18 00:00:00.0000000|22|
|2007-02-19 00:00:00.0000000|52|
|2007-02-20 00:00:00.0000000|60|

[bin()](./binfunction.md) は、多くの言語の [floor()](./floorfunction.md) 関数と同じです。 すべての値が指定した剰余の最も近い倍数にまとめられ、[summarize](./summarizeoperator.md) でグループに行を割り当てることができるようになります。

<a name="displaychartortable"></a>
## <a name="display-a-chart-or-table-render"></a>グラフまたはテーブルを表示する: *render*

2 つの列へのプロジェクションを行い、それらをグラフの x 軸と y 軸として使用することができます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tutorial/event-counts-state.png" alt-text="州別の嵐イベント数の棒グラフを示すスクリーンショット。":::

`project` 操作で `mid` を削除しましたが、その順序でグラフに州を表示する場合はそれがまだ必要です。

厳密に言えば、`render` はクエリ言語の一部ではなくクライアントの機能です。 それでも、言語に統合されており、結果を構想するのに役立ちます。


## <a name="timecharts"></a>時間グラフ

数値ビンに戻り、時系列を表示してみましょう。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tutorial/time-series-start-bin.png" alt-text="日時によってビン分割されたイベントの折れ線グラフのスクリーンショット。":::

## <a name="multiple-series"></a>複数の系列

値の組み合わせごとに別の行を作成する場合は、以下のように、 `summarize by` 句で複数の値を使用します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tutorial/table-count-source.png" alt-text="ソース別のテーブル数を示すスクリーンショット。":::

前の例に `render` を追加するだけです: `| render timechart`。

:::image type="content" source="images/tutorial/line-count-source.png" alt-text="ソース別の折れ線グラフ数を示すスクリーンショット。":::

`render timechart` で最初の列が x 軸として使用され、他の列が個別の線として表示されることに注意してください。

## <a name="daily-average-cycle"></a>日次平均サイクル

ここでは、アクティビティが平均的な日にどのように変わるかを見てみます。

1 日の時間モジュロでイベントをカウントし、時間単位でビン分割します。 ここでは、`bin` ではなく `floor` を使用します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tutorial/time-count-hour.png" alt-text="時間別の時間グラフ数を示すスクリーンショット。":::

現在、`render` を使用すると期間に適切にラベルが付きませんが、代わりに `| render columnchart` を使用できます。

:::image type="content" source="images/tutorial/column-count-hour.png" alt-text="時間別の棒グラフ数を示すスクリーンショット。":::

## <a name="compare-multiple-daily-series"></a>複数の日次系列の比較

ここでは、アクティビティがさまざまな州で時間帯によってどのように変わるかを見てみます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

:::image type="content" source="images/tutorial/time-hour-state.png" alt-text="時間と州別の時間グラフのスクリーンショット。":::

x 軸を期間ではなく時間の数値にするには、`1h` で除算します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tutorial/column-hour-state.png" alt-text="時間と州別の棒グラフを示すスクリーンショット。":::

## <a name="join-data-types"></a>データ型を結合する

2 つの特定のイベントの種類と、それぞれが発生している州を調べるには、どうすればよいでしょうか。

1 番目の `EventType` と 2 番目の `EventType` で嵐イベントを取得し、`State` で 2 つのセットを結合することができます。

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

:::image type="content" source="images/tutorial/join-events-lightning-avalanche.png" alt-text="雷と雪崩のイベントの結合を示すスクリーンショット。":::

## <a name="user-session-example-of-join"></a>*join* のユーザー セッションの例

このセクションでは、`StormEvents` テーブルは使用しません。

各ユーザー セッションの開始と終了が、各セッションの一意の ID でマークされているイベントを含むデータがあるとします。 

各ユーザー セッションの継続時間を調べるには、どうすればよいでしょうか。

`extend` を使用して 2 つのタイムスタンプに別名を指定してから、セッションの継続時間を計算できます。

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

:::image type="content" source="images/tutorial/user-session-extend.png" alt-text="ユーザー セッション拡張の結果のテーブルのスクリーンショット。":::

結合を実行する前に必要な列のみを選択する場合は、`project` を使用することをお勧めします。 同じ句で、`timestamp` 列の名前を変更します。

## <a name="plot-a-distribution"></a>分布のプロット

`StormEvents` テーブルに戻り、長さごとに嵐の発生件数を調べます。

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

:::image type="content" source="images/tutorial/event-count-duration.png" alt-text="期間別のイベント数の時間グラフの結果のスクリーンショット。":::

または、`| render columnchart` を使用することもできます。

:::image type="content" source="images/tutorial/column-event-count-duration.png" alt-text="期間別のイベント数時間グラフの縦棒グラフのスクリーンショット。":::

## <a name="percentiles"></a>パーセンタイル

期間の範囲ごとの嵐の割合はどうなっているでしょうか。

この情報を取得するには、前のクエリを使用して、`render` を次のように置き換えます。

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

この例では、`by` 句を使用しなかったため、出力は単一行になります。

:::image type="content" source="images/tutorial/summarize-percentiles-duration.png" lightbox="images/tutorial/summarize-percentiles-duration.png" alt-text="期間ごとのパーセンタイルに要約した結果のテーブルのスクリーンショット。":::

出力から次のことがわかります。

* 嵐の 5% の期間は 5 分未満である。
* 嵐の 50% の継続時間は 1 時間 25 分未満であった。
* 嵐の 95% は少なくとも 2 時間 50 分続いた。

州ごとの個別の内訳を取得するには、両方の `summarize` 演算子で `state` 列を個別に使用します。

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

:::image type="content" source="images/tutorial/summarize-percentiles-state.png" alt-text="州別のパーセンタイル期間をまとめたテーブル。":::

## <a name="assign-a-result-to-a-variable-let"></a>結果を変数に代入する: *let*

前の `join` の例のクエリ式の各部分を分離するには、[let](./letstatement.md) を使用します。 結果は変わりません。

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
> Kusto Explorer でクエリ全体を実行するには、クエリの各部分の間に空白行を追加しないでください。

## <a name="combine-data-from-several-databases-in-a-query"></a>1 つのクエリで複数のデータベースのデータを結合する

次のクエリの `Logs` テーブルは、既定のデータベースに存在している必要があります。

```kusto
Logs | where ...
```

異なるデータベース内のテーブルにアクセスするには、次の構文を使用します。

```kusto
database("db").Table
```

たとえば、`Diagnostics` および `Telemetry` という名前のデータベースがあり、2 つのテーブルの一部のデータを関連付ける場合は、次のクエリを使用できます (`Diagnostics` が既定のデータベースであるとします)。

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

既定のデータベースが `Telemetry` である場合は、次のクエリを使用します。

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
前に示した 2 つのクエリでは、両方のデータベースが現在接続しているクラスター内にあることを前提としています。 `Telemetry` データベースが *TelemetryCluster.kusto.windows.net* という名前のクラスターにあるとすると、それにアクセスするには、次のクエリを使用します。

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> [!NOTE]
> クラスターを指定するときは、データベースが必須です。

1 つのクエリで複数のデータベースのデータを結合する方法の詳細については、[複数のデータベースにまたがるクエリ](cross-cluster-or-database-queries.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- [Kusto クエリ言語](samples.md?pivots=azuredataexplorer)のコード サンプルを確認してください。

::: zone-end

::: zone pivot="azuremonitor"

Kusto クエリ言語について学習する最善の方法は、いくつかの基本的なクエリを見て、言語の "感覚" をつかむことです。 これらのクエリは、Azure Data Explorer のチュートリアルで使用されているクエリに似ていますが、代わりに Azure Log Analytics ワークスペースの共通テーブルからのデータを使用します。 

Azure portal で Log Analytics を使用して、これらのクエリを実行します。 Log Analytics は、ログ クエリを記述するために使用できるツールです。 Azure Monitor でログ データを使用してから、ログ クエリの結果を評価します。 Log Analytics に慣れていない場合は、[Log Analytics のチュートリアル](/azure/azure-monitor/log-query/log-analytics-tutorial)を済ませてください。

このチュートリアルのすべてのクエリでは、[Log Analytics のデモ環境](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)を使用します。 独自の環境を使用してもかまいませんが、ここで使用されているテーブルの一部がない可能性があります。 デモ環境のデータは静的ではないため、自分で行ったクエリの結果は、ここに示されている結果と多少異なる場合があります。


## <a name="count-rows"></a>行数のカウント

[InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) テーブルには、Azure Monitor for VMs や Azure Monitor for containers のような分析情報によって収集されたパフォーマンス データが含まれています。 テーブルの大きさを調べるため、行数を単純にカウントする演算子にその内容をパイプします。

クエリは、データ ソース (通常はテーブル名) の後に、必要に応じてパイプ文字の 1 つ以上のペアと、表形式演算子を指定したものです。 この場合、`InsightsMetrics` テーブルのすべてのレコードが返された後、[count 演算子](./countoperator.md)に送信されます。 `count` 演算子がクエリの最後のコマンドであるため、その演算子によって結果が表示されます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
InsightsMetrics | count
```

出力は次のようになります。

|Count|
|-----|
|1,263,191|
    

## <a name="filter-by-boolean-expression-where"></a>ブール式でフィルター処理を行う: *where*

[AzureActivity](/azure/azure-monitor/reference/tables/azureactivity) テーブルに含まれる Azure アクティビティ ログのエントリにより、Azure で発生したサブスクリプション レベルまたは管理グループ レベルのイベントに関する分析情報が提供されます。 特定の週の `Critical` エントリだけを表示してみましょう。

[where](/azure/data-explorer/kusto/query/whereoperator) 演算子は Kusto クエリ言語でよく使用されます。 `where` によってテーブルがフィルター処理され、特定の条件に一致する行だけになります。 次の例では、複数のコマンドを使用します。 最初に、クエリでテーブルのすべてのレコードを取得します。 次に、データをフィルター処理し、ある時間範囲内のレコードだけを取得します。 最後に、それらの結果をフィルター処理して、`Critical` レベルのレコードのみを取得します。

> [!NOTE]
> `TimeGenerated` 列を使用してクエリでフィルターを指定するだけでなく、Log Analytics で時間の範囲を指定することもできます。 詳細については、「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](/azure/azure-monitor/log-query/scope)」を参照してください。

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
```

:::image type="content" source="images/tutorial/azure-monitor-where-results.png" lightbox="images/tutorial/azure-monitor-where-results.png" alt-text="where 演算子の例の結果を示すスクリーンショット。":::

## <a name="select-a-subset-of-columns-project"></a>列のサブセットを選択する: *project*

必要な列だけを含めるには、[project](./projectoperator.md) を使用します。 前の例を基にして、出力を特定の列に限定してみましょう。

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
| project TimeGenerated, Level, OperationNameValue, ResourceGroup, _ResourceId
```

:::image type="content" source="images/tutorial/azure-monitor-project-results.png" lightbox="images/tutorial/azure-monitor-project-results.png" alt-text="project 演算子の例の結果を示すスクリーンショット。":::

## <a name="show-n-rows-take"></a>*n* 行を表示する: *take*

[NetworkMonitoring](/azure/azure-monitor/reference/tables/networkmonitoring) には、Azure 仮想ネットワークの監視データが格納されています。 [take](./takeoperator.md) 演算子を使用して、そのテーブル内の 10 行のランダムなサンプル行を調べてみましょう。 [take](./takeoperator.md) を使用すると、テーブルの特定数の行が特定の順序で表示されます。

```kusto
NetworkMonitoring
| take 10
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-take-results.png" lightbox="images/tutorial/azure-monitor-take-results.png" alt-text="take 演算子の例の結果を示すスクリーンショット。":::

## <a name="order-results-sort-top"></a>結果の順序を指定する: *sort*、*top*

最初に時間順に並べ替えることにより、ランダムなレコードではなく、最新の 5 つのレコードを取得できます。

```kusto
NetworkMonitoring
| sort by TimeGenerated desc
| take 5
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

代わりに [top](./topoperator.md) 演算子を使用することで、この動作を正確に実現できます。 

```kusto
NetworkMonitoring
| top 5 by TimeGenerated desc
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-top-results.png" lightbox="images/tutorial/azure-monitor-top-results.png" alt-text="top 演算子の例の結果を示すスクリーンショット。":::

## <a name="compute-derived-columns-extend"></a>派生列を計算する: *extend*

[extend](./projectoperator.md) 演算子は [project](./projectoperator.md) に似ていますが、列のセットを置き換えるのではなく、列のセットに追加します。 両方の演算子を使用して、各行の計算に基づく新しい列を作成できます。

[Perf](/azure/azure-monitor/reference/tables/perf) テーブルには、Log Analytics エージェントが実行されている仮想マシンから収集されたパフォーマンス データが格納されています。 

```kusto
Perf
| where ObjectName == "LogicalDisk" and CounterName == "Free Megabytes"
| project TimeGenerated, Computer, FreeMegabytes = CounterValue
| extend FreeGigabytes = FreeMegabytes / 1000
```

:::image type="content" source="images/tutorial/azure-monitor-extend-results.png" lightbox="images/tutorial/azure-monitor-extend-results.png" alt-text="extend 演算子の例の結果を示すスクリーンショット。":::

## <a name="aggregate-groups-of-rows-summarize"></a>行のグループを集計する: *summarize*

[summarize](./summarizeoperator.md) 演算子を使用すると、`by` 句の値が同じになる行がグループ化されます。 次に、`count` のような集計関数を使用して、各グループを 1 つの行に結合します。 さまざまな[集計関数](./summarizeoperator.md#list-of-aggregation-functions)を使用できます。 1 つの `summarize` 演算子で複数の集計関数を使用して、複数の計算列を生成できます。 

[SecurityEvent](/azure/azure-monitor/reference/tables/securityevent) テーブルには、監視対象のコンピューターで開始されたログオンやプロセスなどのセキュリティ イベントが格納されています。 各コンピューターで発生したイベントの数をレベルごとにカウントできます。 この例では、コンピューターとレベルの組み合わせごとに 1 つの行が生成されます。 列には、イベントの数が格納されます。

```kusto
SecurityEvent
| summarize count() by Computer, Level
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-count-results.png" lightbox="images/tutorial/azure-monitor-summarize-count-results.png" alt-text="summarize count 演算子の例の結果を示すスクリーンショット。":::

## <a name="summarize-by-scalar-values"></a>スカラー値による集計

数や時刻の値のようなスカラー値で集計できますが、 [bin()](./binfunction.md) 関数を使用して、行を個別のデータ セットにグループ化する必要があります。 たとえば、`TimeGenerated` で集計すると、ほぼすべての時刻値に対して行が取得されます。 それらの値を時刻や日でまとめるには、`bin()` を使用します。

[InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) テーブルには、Azure Monitor for VMs や Azure Monitor for containers のような分析情報によって収集されたパフォーマンス データが含まれています。 次のクエリを実行すると、複数のコンピューターについて 1 時間ごとの平均プロセッサ使用率が表示されます。

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-avg-results.png" lightbox="images/tutorial/azure-monitor-summarize-avg-results.png" alt-text="avg 演算子の例の結果を示すスクリーンショット。":::

## <a name="display-a-chart-or-table-render"></a>グラフまたはテーブルを表示する: *render*

[render](./renderoperator.md?pivots=azuremonitor) 演算子では、クエリの出力のレンダリング方法を指定します。 既定では、Log Analytics による出力はテーブルとしてレンダリングされます。 クエリを実行した後、さまざまな種類のグラフを選択できます。 特定のグラフの種類が通常優先されるクエリには、`render` 演算子を含めると便利です。

次の例を実行すると、1 つのコンピューターについて 1 時間ごとの平均プロセッサ使用率が表示されます。 出力は時間グラフとしてレンダリングされます。

```kusto
InsightsMetrics
| where Computer == "DC00.NA.contosohotels.com"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-results.png" lightbox="images/tutorial/azure-monitor-render-results.png" alt-text="render 演算子の例の結果を示すスクリーンショット。":::

## <a name="work-with-multiple-series"></a>複数の系列を使用する

`summarize by` 句で複数の値を使用すると、グラフには、値のセットごとに個別の系列が表示されます。

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-multiple-results.png" lightbox="images/tutorial/azure-monitor-render-multiple-results.png" alt-text="複数の系列がある render 演算子の例の結果を示すスクリーンショット。":::

## <a name="join-data-from-two-tables"></a>2 つのテーブルのデータを結合する

1 つのクエリで 2 つのテーブルからデータを取得する必要がある場合はどうすればよいでしょうか。 [join](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor) 演算子を使用すると、複数のテーブルの行を 1 つの結果セットに結合できます。 join で一致させる行がわかるように、各テーブルに一致する値を持つ列が含まれる必要があります。

[VMComputer](/azure/azure-monitor/reference/tables/vmcomputer) は、監視対象の仮想マシンに関する詳細を格納するために Azure Monitor で VM に使用されるテーブルです。 [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) には、それらの仮想マシンから収集されたパフォーマンス データが格納されます。 *InsightsMetrics* に収集される 1 つの値は使用可能なメモリであり、使用可能なメモリの割合ではありません。 割合を計算するには、各仮想マシンの物理メモリが必要です。 その値は `VMComputer` にあります。

次のクエリ例では、join を使用してこの計算を実行します。 詳細は各コンピューターから定期的に収集されるため、[distinct](/azure/data-explorer/kusto/query/distinctoperator) 演算子が `VMComputer` で使用されています。 その結果、テーブルのコンピューターごとに複数の行が作成されます。 2 つのテーブルは、`Computer` 列を使用して結合されます。 `VMComputer` の `Computer` 列の値と一致する `Computer` の値を使用して、`InsightsMetrics` の行ごとに両方のテーブルの列を含む行が結果セットに作成されます。

```kusto
VMComputer
| distinct Computer, PhysicalMemoryMB
| join kind=inner (
    InsightsMetrics
    | where Namespace == "Memory" and Name == "AvailableMB"
    | project TimeGenerated, Computer, AvailableMemoryMB = Val
) on Computer
| project TimeGenerated, Computer, PercentMemory = AvailableMemoryMB / PhysicalMemoryMB * 100
```

:::image type="content" source="images/tutorial/azure-monitor-join-results.png" lightbox="images/tutorial/azure-monitor-join-results.png" alt-text="join 演算子の例の結果を示すスクリーンショット。":::

## <a name="assign-a-result-to-a-variable-let"></a>結果を変数に代入する: *let*

クエリの読み取りと管理を簡単にするには、[let](./letstatement.md) を使用します。 この演算子を使用すると、変数にクエリの結果を代入し、後で使用することができます。 `let` ステートメントを使用すると、前の例のクエリを次のように書き換えることができます。

 
```kusto
let PhysicalComputer = VMComputer
    | distinct Computer, PhysicalMemoryMB;
    let AvailableMemory = 
InsightsMetrics
    | where Namespace == "Memory" and Name == "AvailableMB"
    | project TimeGenerated, Computer, AvailableMemoryMB = Val;
PhysicalComputer
| join kind=inner (AvailableMemory) on Computer
| project TimeGenerated, Computer, PercentMemory = AvailableMemoryMB / PhysicalMemoryMB * 100
```

:::image type="content" source="images/tutorial/azure-monitor-let-results.png" lightbox="images/tutorial/azure-monitor-let-results.png" alt-text="let 演算子の例の結果を示すスクリーンショット。":::

## <a name="next-steps"></a>次の手順

- [Kusto クエリ言語](samples.md?pivots=azuremonitor)のコード サンプルを確認してください。


::: zone-end
