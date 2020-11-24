---
title: 'チュートリアル: Azure データエクスプローラー & の Kusto クエリ Azure Monitor'
description: このチュートリアルでは、Kusto クエリ言語でクエリを使用して、Azure データエクスプローラーおよび Azure Monitor での一般的なクエリのニーズを満たす方法について説明します。
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
ms.openlocfilehash: 8a47c51aa7924a28b27602056ea869bfd7a09936
ms.sourcegitcommit: faa747df81c49b96d173dbd5a28d2ca4f3a2db5f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "95783724"
---
# <a name="tutorial-use-kusto-queries-in-azure-data-explorer-and-azure-monitor"></a>チュートリアル: Azure データエクスプローラーおよび Azure Monitor で Kusto クエリを使用する

::: zone pivot="azuredataexplorer"

Kusto クエリ言語について学習する最善の方法は、いくつかの基本的なクエリを見て、言語の "感覚" を得ることです。 [いくつかのサンプルデータを含むデータベース](https://help.kusto.windows.net/Samples)を使用することをお勧めします。 このチュートリアルで示されているクエリは、そのデータベースで実行する必要があります。 サンプルデータベースのテーブルには、 `StormEvents` 米国で発生したストームに関する情報が記載されています。

## <a name="count-rows"></a>行数のカウント

このサンプルデータベースには、という名前のテーブルがあり `StormEvents` ます。 テーブルのサイズを調べるには、テーブル内の行をカウントするだけの操作にその内容をパイプします。 

*構文メモ*: クエリは、データソース (通常はテーブル名) で、必要に応じてパイプ文字の1つ以上のペアと、表形式演算子を指定します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

出力は次のようになります。

|Count|
|-----|
|59066|
    
詳細については、「 [count 演算子](./countoperator.md)」を参照してください。

## <a name="select-a-subset-of-columns-project"></a>列のサブセットの選択: *プロジェクト*

必要な列だけを取得するには、 [project](./projectoperator.md) を使用します。 [プロジェクト](./projectoperator.md)と[take](./takeoperator.md)演算子の両方を使用する次の例を参照してください。

## <a name="filter-by-boolean-expression-where"></a>ブール式によるフィルター: *where*

`flood`2007 年2月ののイベントのみを確認してみましょう `California` 。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

出力は次のようになります。

|StartTime|EndTime|州|EventType|EpisodeNarrative|
|---|---|---|---|---|
|2007-02-19 00:00: 00.0000000|2007-02-19 08:00: 00.0000000|カリフォルニア|洪水|南のサンホアキンバレーで正面システムを移動すると、午前19時の午前1時に、雨の雨を西洋のに簡単に移行できます。 軽微なフラッディングは、Taft 付近の州幹線道路166で報告されています。|

## <a name="show-n-rows-take"></a>*N* 行の表示: *take*

いくつかのデータを見てみましょう。 5行のランダムサンプルの内容

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

出力は次のようになります。

|StartTime|EndTime|EventType|州|EventNarrative|
|---|---|---|---|---|
|2007-09-18 20:00: 00.0000000|2007-09-19 18:00: 00.0000000|重い雨|フロリダ|Coastal Volusia 郡の一部にわたって24時間のうち9インチの雨がいます。|
|2007-09-20 21:57: 00.0000000|2007-09-20 22:05: 00.0000000|Tornado|フロリダ|Eustis 町で触れた tornado は、西 Crooked Lake の北端にあります。 Tornado は、北北西を Eustis 移動したときに EF1 の強さをすばやく極めるします。 このトラックは、長さが2マイル未満で、最大幅が300ヤードになっています。  Tornado は7本の自宅を破壊しています。 20個の自宅が、大きなダメージを受け、81の自宅が軽微な損害を報告しました。 重大な負傷や、プロパティの破損は $620万に設定されていました。|
|2007-09-29 08:11: 00.0000000|2007-09-29 08:11: 00.0000000|Waterspout|大西洋南部|メルボルンの東南南東部に形成され、waterspout が簡単に海岸に移行しました。|
|2007-12-20 07:50: 00.0000000|2007-12-20 07:53: 00.0000000|雷雨風|MISSISSIPPI|多数の大規模なツリーが、いくつかダウンしています。 東 Adams 郡で破損が発生しました。|
|2007-12-30 16:00: 00.0000000|2007-12-30 16:05: 00.0000000|雷雨風|グルジア|郡のディスパッチでは、複数のツリーが、州道路206付近の Quincey Batten ループに沿って表示されました。 ツリーの削除コストが推定されました。|

ただし、テーブルの [行は特定](./takeoperator.md) の順序で表示されないので、並べ替えてみましょう。 ([limit](./takeoperator.md) は [take](./takeoperator.md) のエイリアスであり、同じ効果があります)。

## <a name="order-results-sort-top"></a>順序の結果: *sort*、 *top*

* *構文* に関する注意: 一部の演算子には、のようなキーワードで導入されたパラメーターがあり `by` ます。
* 次の例では、注文が降順に並べ替えられ、結果が昇順で並べ替えら `desc` `asc` れます。

特定の列で並べ替えられた最初の *n* 行を表示します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

出力は次のようになります。

|StartTime|EndTime|EventType|州|EventNarrative|
|---|---|---|---|---|
|2007-12-31 22:30: 00.0000000|2007-12-31 23:59: 00.0000000|冬の嵐|ミシガン|この大きな雪のイベントは、新年の朝に続きます。|
|2007-12-31 22:30: 00.0000000|2007-12-31 23:59: 00.0000000|冬の嵐|ミシガン|この大きな雪のイベントは、新年の朝に続きます。|
|2007-12-31 22:30: 00.0000000|2007-12-31 23:59: 00.0000000|冬の嵐|ミシガン|この大きな雪のイベントは、新年の朝に続きます。|
|2007-12-31 23:53: 00.0000000|2007-12-31 23:53: 00.0000000|高風|カリフォルニア|北から北東への風 gusting と約 58 mph が Ventura 郡の山で報告されました。|
|2007-12-31 23:53: 00.0000000|2007-12-31 23:53: 00.0000000|高風|カリフォルニア|ウォームスプリング RAWS センサーは、northerly 風 gusting を 58 mph に報告しました。|

[Sort](./sortoperator.md)を使用して同じ結果を得ることができます。その後、次を[実行](./takeoperator.md)します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndTime, EventType, EventNarrative
```

## <a name="compute-derived-columns-extend"></a>計算派生列: *拡張*

すべての行の値を計算して、新しい列を作成します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

出力は次のようになります。

|StartTime|EndTime|Duration|EventType|州|
|---|---|---|---|---|
|2007-09-18 20:00: 00.0000000|2007-09-19 18:00: 00.0000000|22:00:00|重い雨|フロリダ|
|2007-09-20 21:57: 00.0000000|2007-09-20 22:05: 00.0000000|00:08:00|Tornado|フロリダ|
|2007-09-29 08:11: 00.0000000|2007-09-29 08:11: 00.0000000|00:00:00|Waterspout|大西洋南部|
|2007-12-20 07:50: 00.0000000|2007-12-20 07:53: 00.0000000|00:03:00|雷雨風|MISSISSIPPI|
|2007-12-30 16:00: 00.0000000|2007-12-30 16:05: 00.0000000|00:05:00|雷雨風|グルジア|

列名を再利用し、計算結果を同じ列に割り当てることができます。

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

[スカラー式](./scalar-data-types/index.md) には、通常の演算子 (、、、、) をすべて含めることができ、 `+` 便利な `-` `*` `/` `%` 関数の範囲を使用できます。

## <a name="aggregate-groups-of-rows-summarize"></a>行の集計グループ: *要約*

各国で発生したイベントの数をカウントします。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count = count() by State
```

句で同じ値を持つ行[をグループ化](./summarizeoperator.md) `by` し、集計関数 (など) を使用して、 `count` 各グループを1つの行に結合します。 この例では、状態ごとに1行の行があり、その状態の行の数を示す列があります。

さまざまな [集計関数](./summarizeoperator.md#list-of-aggregation-functions) を使用できます。 1つの演算子でいくつかの集計関数を使用すると、複数の `summarize` 計算列を作成できます。 たとえば、各州のストームの数や、州ごとの種類が一意であるストームの合計を取得できます。 次に、 [top](./topoperator.md) を使用して、最もストームに影響する状態を取得できます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

出力は次のようになります。

|州|StormCount|TypeOfStorms|
|---|---|---|
|テキサス州|4701|27|
|カンザス|3166|21|
|アイオワ州|2337|19|
|イリノイ州|2022|23|
|州|2016|20|

演算子の結果では、次のようになり `summarize` ます。

* 各列には、という名前が付けられ `by` ます。
* 各計算式には列があります。
* 値の各組み合わせ `by` には行があります。

## <a name="summarize-by-scalar-values"></a>スカラー値による集計

句ではスカラー (数値、時刻、または間隔) の値を使用でき `by` ますが、 [bin ()](./binfunction.md) 関数を使用して値をビンに入れることをお勧めします。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

このクエリでは、すべてのタイムスタンプが1日の間隔に短縮されます。

|StartTime|event_count|
|---|---|
|2007-02-14 00:00: 00.0000000|180|
|2007-02-15 00:00: 00.0000000|66|
|2007-02-16 00:00: 00.0000000|164|
|2007-02-17 00:00: 00.0000000|103|
|2007-02-18 00:00: 00.0000000|22|
|2007-02-19 00:00: 00.0000000|52|
|2007-02-20 00:00: 00.0000000|60|

[Bin ()](./binfunction.md)は、多くの言語の[floor ()](./floorfunction.md)関数と同じです。 これにより、すべての値が、指定した剰余の倍数になるように単純化されるので、 [集計](./summarizeoperator.md) によって行をグループに割り当てることができます。

<a name="displaychartortable"></a>
## <a name="display-a-chart-or-table-render"></a>グラフまたはテーブルを表示する: *render*

2つの列を射影して、グラフの x 軸と y 軸として使用することができます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tutorial/event-counts-state.png" alt-text="状態別のストームイベント数の縦棒グラフを示すスクリーンショット。":::

操作を削除しました `mid` `project` が、グラフでその注文の国を表示する必要がある場合は、それも必要です。

厳密に言うと、 `render` はクエリ言語の一部ではなく、クライアントの機能です。 それでも、言語に統合されており、結果を構想するのに役立ちます。


## <a name="timecharts"></a>時間グラフ

数値ビンに戻ると、タイムシリーズが表示されるようになります。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tutorial/time-series-start-bin.png" alt-text="時間によって分割されたイベントの折れ線グラフのスクリーンショット。":::

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

`render`前の例のように、という用語を追加 `| render timechart` します。

:::image type="content" source="images/tutorial/line-count-source.png" alt-text="ソース別の折れ線グラフの数を示すスクリーンショット。":::

では `render timechart` 最初の列が x 軸として使用され、他の列は個別の行として表示されることに注意してください。

## <a name="daily-average-cycle"></a>日次平均サイクル

活動は平均1日にどのように変化しますか。

1日の時間単位でイベントをカウントします。 ここでは、の代わりにを使用し `floor` `bin` ます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tutorial/time-count-hour.png" alt-text="線上数を時間単位で示すスクリーンショット。":::

現在、では、 `render` 期間に適切にラベル付けされませんが、代わりにを使用でき `| render columnchart` ます。

:::image type="content" source="images/tutorial/column-count-hour.png" alt-text="1時間ごとの縦棒グラフを示すスクリーンショット。":::

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

:::image type="content" source="images/tutorial/time-hour-state.png" alt-text="時間と州別の線上のスクリーンショット。":::

X 軸を継続時間では `1h` なく時間数に変換するには、を除算します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tutorial/column-hour-state.png" alt-text="時間と州別の縦棒グラフを示すスクリーンショット。":::

## <a name="join-data-types"></a>データ型の結合

2つの特定のイベントの種類を見つけて、それぞれの状態を確認するには、どうすればよいでしょうか。

1番目と2番目のを使用して嵐イベントを取得し、次の2つのセットを結合することができ `EventType` `EventType` `State` ます。

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

:::image type="content" source="images/tutorial/join-events-lightning-avalanche.png" alt-text="イベントの稲妻と大量の結合を示すスクリーンショット。":::

## <a name="user-session-example-of-join"></a>ユーザーセッションの *結合* の例

このセクションでは、テーブルは使用しません `StormEvents` 。

各ユーザーセッションの開始と終了を、各セッションの一意の ID でマークするイベントを含むデータがあるとします。 

各ユーザーセッションの継続時間を確認するには、どうすればよいでしょうか。

を使用して、 `extend` 2 つのタイムスタンプのエイリアスを指定し、セッション継続時間を計算することができます。

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

:::image type="content" source="images/tutorial/user-session-extend.png" alt-text="ユーザーセッションが拡張した結果のテーブルのスクリーンショット。":::

結合を実行する前に、を使用して必要な列のみを選択することをお勧めし `project` ます。 同じ句で、列の名前を変更 `timestamp` します。

## <a name="plot-a-distribution"></a>分布のプロット

テーブルに戻る `StormEvents` と、長さが異なるのはどのくらいのストームがあるのでしょうか。

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

:::image type="content" source="images/tutorial/event-count-duration.png" alt-text="期間別のイベント数の線上結果のスクリーンショット。":::

または、次のものを使用でき `| render columnchart` ます。

:::image type="content" source="images/tutorial/column-event-count-duration.png" alt-text="イベント数の線上の列グラフのスクリーンショット (期間別)。":::

## <a name="percentiles"></a>パーセンタイル

さまざまなストームの割合で検出される期間の範囲を教えてください。

この情報を取得するには、前のクエリを使用しますが、は `render` 次のように置き換えます。

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

この場合、句を使用していないので、 `by` 出力は単一行になります。

:::image type="content" source="images/tutorial/summarize-percentiles-duration.png" lightbox="images/tutorial/summarize-percentiles-duration.png" alt-text="パーセンタイルを期間ごとに要約する結果のテーブルのスクリーンショット。":::

次のような出力が表示されます。

* 嵐の5% は、期間が5分未満です。
* 嵐の50% が1時間と25分未満です。
* 5% の嵐が、少なくとも2時間と50分で継続しています。

状態ごとに個別の内訳を取得するには、次の両方の演算子を使用して列を個別に使用し `state` `summarize` ます。

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

:::image type="content" source="images/tutorial/summarize-percentiles-state.png" alt-text="テーブルは、状態別のパーセンタイル期間の概要を示します。":::

## <a name="assign-a-result-to-a-variable-let"></a>変数に結果を割り当てる: *let*

前の例のクエリ式の各部分を分割するには、 [let](./letstatement.md) を使用し `join` ます。 結果は変わりません。

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
> Kusto エクスプローラーでクエリ全体を実行するには、クエリの一部の間に空白行を追加しないでください。

## <a name="combine-data-from-several-databases-in-a-query"></a>クエリ内の複数のデータベースのデータを結合する

次のクエリでは、 `Logs` テーブルが既定のデータベースに存在している必要があります。

```kusto
Logs | where ...
```

別のデータベース内のテーブルにアクセスするには、次の構文を使用します。

```kusto
database("db").Table
```

たとえば、という名前のデータベースがあり、2つのテーブルの一部のデータを関連付ける場合は、 `Diagnostics` `Telemetry` 次のクエリを使用することができ `Diagnostics` ます (既定のデータベースであると仮定します)。

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

既定のデータベースがの場合は、次のクエリを使用し `Telemetry` ます。

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
上記の2つのクエリでは、両方のデータベースが現在接続しているクラスター内にあることを前提としています。 データベースが `Telemetry` *TelemetryCluster.kusto.windows.net* という名前のクラスターにある場合は、次のクエリを使用してアクセスします。

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> [!NOTE]
> クラスターが指定されている場合、データベースは必須です。

クエリで複数のデータベースのデータを結合する方法の詳細については、「複数 [データベースにまたがるクエリ](cross-cluster-or-database-queries.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Kusto クエリ言語](samples.md?pivots=azuredataexplorer)のコードサンプルを表示します。

::: zone-end

::: zone pivot="azuremonitor"

Kusto クエリ言語について学習する最善の方法は、いくつかの基本的なクエリを見て、言語の "感覚" を得ることです。 これらのクエリは、Azure データエクスプローラーチュートリアルで使用されるクエリに似ていますが、代わりに Azure Log Analytics ワークスペースの共通テーブルからデータを使用します。 

Azure portal の Log Analytics を使用して、これらのクエリを実行します。 Log Analytics は、ログクエリを記述するために使用できるツールです。 Azure Monitor でログデータを使用し、ログクエリの結果を評価します。 Log Analytics に慣れていない場合は、 [Log Analytics チュートリアル](/azure/azure-monitor/log-query/log-analytics-tutorial)を完了してください。

このチュートリアルのすべてのクエリでは、 [Log Analytics デモ環境](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)を使用します。 独自の環境を使用できますが、ここで使用されているテーブルの一部が含まれていない可能性があります。 デモ環境のデータは静的ではないため、クエリの結果はここに示されている結果とは多少異なる場合があります。


## <a name="count-rows"></a>行数のカウント

[InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics)テーブルには、コンテナーの Azure Monitor for VMs や Azure Monitor などの洞察によって収集されたパフォーマンスデータが含まれています。 テーブルのサイズを調べるには、その内容を、単に行数をカウントする演算子にパイプします。

クエリは、データソース (通常はテーブル名) で、必要に応じてパイプ文字の1つ以上のペアと、表形式演算子を指定します。 この場合、テーブルのすべてのレコード `InsightsMetrics` が返され、 [count 演算子](./countoperator.md)に送信されます。 演算子は `count` クエリの最後のコマンドであるため、演算子は結果を表示します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
InsightsMetrics | count
```

出力は次のようになります。

|Count|
|-----|
|1263191|
    

## <a name="filter-by-boolean-expression-where"></a>ブール式によるフィルター: *where*

[Azureactivity](/azure/azure-monitor/reference/tables/azureactivity)テーブルには、azure で発生したサブスクリプションレベルまたは管理グループレベルのイベントに関する洞察を提供する、azure アクティビティログのエントリが含まれています。 特定の週のエントリのみを表示してみましょう `Critical` 。

[Where](/azure/data-explorer/kusto/query/whereoperator)演算子は Kusto クエリ言語で共通です。 `where` 特定の条件に一致する行にテーブルをフィルター処理します。 次の例では、複数のコマンドを使用します。 まず、クエリはテーブルのすべてのレコードを取得します。 次に、時間範囲内のレコードについてのみデータをフィルター処理します。 最後に、レベルを持つレコードのみを対象として結果をフィルター処理 `Critical` します。

> [!NOTE]
> 列を使用してクエリにフィルターを指定するだけでなく `TimeGenerated` 、Log Analytics で時間の範囲を指定することもできます。 詳細については、 [Azure Monitor Log Analytics の「ログクエリのスコープと時間範囲](/azure/azure-monitor/log-query/scope)」を参照してください。

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
```

:::image type="content" source="images/tutorial/azure-monitor-where-results.png" lightbox="images/tutorial/azure-monitor-where-results.png" alt-text="Where 演算子の例の結果を示すスクリーンショット。":::

## <a name="select-a-subset-of-columns-project"></a>列のサブセットの選択: *プロジェクト*

必要な列のみを含めるには、 [project](./projectoperator.md) を使用します。 前の例を基にして、出力を特定の列に限定してみましょう。

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
| project TimeGenerated, Level, OperationNameValue, ResourceGroup, _ResourceId
```

:::image type="content" source="images/tutorial/azure-monitor-project-results.png" lightbox="images/tutorial/azure-monitor-project-results.png" alt-text="プロジェクト演算子の例の結果を示すスクリーンショット。":::

## <a name="show-n-rows-take"></a>*N* 行の表示: *take*

[Networkmonitoring](/azure/azure-monitor/reference/tables/networkmonitoring) には、Azure virtual network の監視データが含まれています。 [Take](./takeoperator.md)演算子を使用して、そのテーブル内の5つのランダムなサンプル行を確認してみましょう。 [Take](./takeoperator.md)は、特定の順序でテーブルから特定の数の行を表示します。

```kusto
NetworkMonitoring
| take 10
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-take-results.png" lightbox="images/tutorial/azure-monitor-take-results.png" alt-text="Take 演算子の例の結果を示すスクリーンショット。":::

## <a name="order-results-sort-top"></a>順序の結果: *sort*、 *top*

ランダムなレコードではなく、最初に時間順に並べ替えて、最新の5つのレコードを返すことができます。

```kusto
NetworkMonitoring
| sort by TimeGenerated desc
| take 5
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

この正確な動作を取得するには、代わりに [top](./topoperator.md) 演算子を使用します。 

```kusto
NetworkMonitoring
| top 5 by TimeGenerated desc
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-top-results.png" lightbox="images/tutorial/azure-monitor-top-results.png" alt-text="Top 演算子の例の結果を示すスクリーンショット。":::

## <a name="compute-derived-columns-extend"></a>計算派生列: *拡張*

[Extend](./projectoperator.md)演算子は[project](./projectoperator.md)に似ていますが、列を置き換えるのではなく、列のセットに追加します。 両方の演算子を使用すると、各行の計算に基づいて新しい列を作成できます。

パフォーマンス [テーブルに](/azure/azure-monitor/reference/tables/perf) は、Log Analytics エージェントを実行する仮想マシンから収集されたパフォーマンスデータが含まれています。 

```kusto
Perf
| where ObjectName == "LogicalDisk" and CounterName == "Free Megabytes"
| project TimeGenerated, Computer, FreeMegabytes = CounterValue
| extend FreeGigabytes = FreeMegabytes / 1000
```

:::image type="content" source="images/tutorial/azure-monitor-extend-results.png" lightbox="images/tutorial/azure-monitor-extend-results.png" alt-text="拡張演算子の例の結果を示すスクリーンショット。":::

## <a name="aggregate-groups-of-rows-summarize"></a>行の集計グループ: *要約*

[集計](./summarizeoperator.md)演算子は、句内で同じ値を持つ行をグループ化し `by` ます。 次に、のような集計関数を使用して、 `count` 各グループを1つの行に結合します。 さまざまな [集計関数](./summarizeoperator.md#list-of-aggregation-functions) を使用できます。 1つの演算子でいくつかの集計関数を使用すると、複数の `summarize` 計算列を作成できます。 

[Securityevent](/azure/azure-monitor/reference/tables/securityevent)テーブルには、監視対象コンピューターで開始されたログオンやプロセスなどのセキュリティイベントが含まれています。 各コンピューターで発生した各レベルのイベントの数をカウントできます。 この例では、コンピューターとレベルの組み合わせごとに1つの行が生成されます。 列には、イベントの数が格納されます。

```kusto
SecurityEvent
| summarize count() by Computer, Level
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-count-results.png" lightbox="images/tutorial/azure-monitor-summarize-count-results.png" alt-text="Count 演算子の集計の例の結果を示すスクリーンショット。":::

## <a name="summarize-by-scalar-values"></a>スカラー値による集計

数値や時刻の値などのスカラー値で集計できますが、 [bin ()](./binfunction.md) 関数を使用して、行を個別のデータセットにグループ化する必要があります。 たとえば、によって集計した場合、 `TimeGenerated` ほぼすべての時刻値に対して行が取得されます。 `bin()`これらの値を1時間または1日に統合するために使用します。

[InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics)テーブルには、コンテナーの Azure Monitor for VMs や Azure Monitor などの洞察によって収集されたパフォーマンスデータが含まれています。 次のクエリは、複数のコンピューターの1時間ごとの平均プロセッサ使用率を示しています。

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-avg-results.png" lightbox="images/tutorial/azure-monitor-summarize-avg-results.png" alt-text="Avg 演算子の例の結果を示すスクリーンショット。":::

## <a name="display-a-chart-or-table-render"></a>グラフまたはテーブルを表示する: *render*

[Render](./renderoperator.md?pivots=azuremonitor)操作は、クエリの出力をどのように表示するかを指定します。 既定では、Log Analytics は出力をテーブルとしてレンダリングします。 クエリを実行した後で、さまざまなグラフの種類を選択できます。 演算子を使用すると、 `render` 特定のグラフの種類が通常は優先されるクエリにを含めることができます。

次の例は、1台のコンピューターの平均プロセッサ使用率を示しています。 出力は線上としてレンダリングされます。

```kusto
InsightsMetrics
| where Computer == "DC00.NA.contosohotels.com"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-results.png" lightbox="images/tutorial/azure-monitor-render-results.png" alt-text="Render 操作の例の結果を示すスクリーンショット。":::

## <a name="work-with-multiple-series"></a>複数の系列を操作する

句で複数の値を使用する場合 `summarize by` 、グラフには、値のセットごとに個別の系列が表示されます。

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-multiple-results.png" lightbox="images/tutorial/azure-monitor-render-multiple-results.png" alt-text="複数の系列の例を含む render 操作の結果を示すスクリーンショット。":::

## <a name="join-data-from-two-tables"></a>2つのテーブルのデータを結合する

1つのクエリで2つのテーブルからデータを取得する必要がある場合はどうすればよいでしょうか。 [結合](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor)演算子を使用すると、1つの結果セット内の複数のテーブルの行を結合できます。 各テーブルには、一致する行が含まれるように、一致する値を持つ列が必要です。

[Vmcomputer](/azure/azure-monitor/reference/tables/vmcomputer) は、監視している仮想マシンに関する詳細を vm に格納 Azure Monitor テーブルです。 [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) は、これらの仮想マシンから収集されたパフォーマンスデータを格納します。 *InsightsMetrics* で収集される1つの値は使用可能なメモリですが、使用可能なメモリの割合はありません。 割合を計算するには、各仮想マシンの物理メモリが必要です。 この値はに `VMComputer` あります。

次のクエリ例では、結合を使用してこの計算を実行します。 [Distinct](/azure/data-explorer/kusto/query/distinctoperator)演算子は、 `VMComputer` 各コンピューターから詳細情報が定期的に収集されるため、と共に使用されます。 その結果、テーブル内の各コンピューターに対して複数の行が作成されます。 2つのテーブルは、列を使用して結合され `Computer` ます。 の各行に対して両方のテーブルの列を含む行が結果セットに作成されます。の値は、の列の値と同じになり `InsightsMetrics` `Computer` `Computer` `VMComputer` ます。

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

:::image type="content" source="images/tutorial/azure-monitor-join-results.png" lightbox="images/tutorial/azure-monitor-join-results.png" alt-text="結合演算子の例の結果を示すスクリーンショット。":::

## <a name="assign-a-result-to-a-variable-let"></a>変数に結果を割り当てる: *let*

クエリの読み取りと管理を容易にするために、 [let](./letstatement.md) を使用します。 この演算子を使用すると、後で使用できる変数にクエリの結果を割り当てることができます。 ステートメントを使用する `let` と、前の例のクエリを次のように書き換えることができます。

 
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

:::image type="content" source="images/tutorial/azure-monitor-let-results.png" lightbox="images/tutorial/azure-monitor-let-results.png" alt-text="Let 演算子の例の結果を示すスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

- [Kusto クエリ言語](samples.md?pivots=azuremonitor)のコードサンプルを表示します。


::: zone-end
