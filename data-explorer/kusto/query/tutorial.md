---
title: チュートリアル - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのチュートリアルについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
zone_pivot_group_filename: kusto/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: f8a5a7b8f96fdd75ccff5dcfe8499ab98a2d4380
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663336"
---
# <a name="tutorial"></a>チュートリアル

::: zone pivot="azuredataexplorer"

Kusto クエリ言語について学ぶ最も良い方法は、サンプル データを含むデータベースを使用して、その言語の "感じ" を得るための簡単なクエリ[を見ること](https://help.kusto.windows.net/Samples)です。 この記事で説明したクエリは、そのデータベースで実行する必要があります。 この`StormEvents`サンプル データベースのテーブルには、米国で発生した嵐に関するいくつかの情報が示されています。

<!--
  TODO: Provide link to reference data we used originally in StormEvents
-->

<!--
  TODO: A few samples below reference non-existent tables, such as Events and Logs.
        We need to add these tables.
-->

## <a name="count-rows"></a>行数のカウント

このサンプル データベースには、`StormEvents`というテーブルがあります。
その大きさを調べるには、その内容を単に行数を数える演算子にパイプします。

* *構文:* クエリはデータ ソース (通常はテーブル名) で、オプションでパイプ文字と表形式の演算子のペアが 1 つ以上続きます。

```kusto
StormEvents | count
```

結果は次のとおりです。

|Count|
|-----|
|59066|
    
[カウント演算子](./countoperator.md)。

## <a name="project-select-a-subset-of-columns"></a>プロジェクト: 列のサブセットを選択します

[プロジェクト](./projectoperator.md)を使用して、必要な列だけを選択します。 [プロジェクト](./projectoperator.md)とテイク演算子の両方を使用する以下[の例を](./takeoperator.md)参照してください。

## <a name="where-filtering-by-a-boolean-expression"></a>ここで: ブール式によるフィルター処理

2007年2`flood`月から`California`2007年の間にだけ見てみましょう:

```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

|StartTime|EndTime|State|EventType|エピソードナラティブ|
|---|---|---|---|---|
|2007-02-19 00:00:00.0000000|2007-02-19 08:00:00.0000000|カリフォルニア|洪水|南サンホアキンバレーを横切る前線システムは、19日の早朝にカーン郡西部に短い大雨をもたらしました。 小さな洪水は、タフト近くの州道166号線を横切って報告されました。|

## <a name="take-show-me-n-rows"></a>テイク:私にn行を見せてください

たとえば、以下のように 5 個の行を表示するとします。

```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|EventType|State|EventNarrative|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|大雨|フロリダ|沿岸のヴォルシア郡の一部で24時間で9インチもの雨が降りました。|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|竜巻|フロリダ|西曲がった湖の北端にあるユースティスの町で竜巻が降り立った。 竜巻は、ユースティスを北西に移動するにつれて、EF1の強さに急速に強まった。 トラックはわずか2マイルの長さで、300ヤードの最大幅を持っていました。  竜巻は7つの家を破壊しました。 27軒の家屋が大きな被害を受け、81軒が軽微な被害を報告した。 重傷はなく、物的損害は620万ドルに設定されました。|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|竜巻|アトランティック サウス|メルボルンビーチの南東の大西洋に形成されたウォータースパウトは、海岸に向かって一時的に移動しました。|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|雷雨風|ミシシッピ|送電線の一部を倒して、多数の大きな木が吹き倒された。 被害は東部アダムズ郡で発生しました。|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|雷雨風|グルジア|郡の派遣は、州道206の近くのクインシーバッテンループに沿っていくつかの木が吹き飛ばされたと報告しました。 木の除去のコストが見積もられました。|

しかし、[テーブル](./takeoperator.md)の行を順不同で表示するので、並べ替えてみましょう。
* [limit](./takeoperator.md)は[テイク](./takeoperator.md)のエイリアスであり、同じ効果があります。

## <a name="sort-and-top"></a>並べ替えとトップ

* *構文:* 演算子によっては、 などの`by`キーワードによって導入されたパラメーターがあります。
* `desc` は降順、`asc` は昇順を意味します。

特定の列で並べ替えた最初の n 個の行を表示する場合は、以下のように指定します。

```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|EventType|State|EventNarrative|
|---|---|---|---|---|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|冬の嵐|ミシガン|この大雪イベントは元日の早朝に続きました。|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|冬の嵐|ミシガン|この大雪イベントは元日の早朝に続きました。|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|冬の嵐|ミシガン|この大雪イベントは元日の早朝に続きました。|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|強風|カリフォルニア|ベンチュラ郡の山岳地帯では、時速約58マイルに突き出た北から北東の風が報告された。|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|強風|カリフォルニア|ウォームスプリングスRAWSセンサーは、時速58マイルに突き出た北風を報告しました。|

[ソート](./sortoperator.md)を使用して、演算子を[取ることによって](./takeoperator.md)同じを達成することができます

```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndLat, EventType, EventNarrative
```

## <a name="extend-compute-derived-columns"></a>拡張: 派生列の計算

すべての行の値を計算して新しい列を作成します。

```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

|StartTime|EndTime|Duration|EventType|State|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|22:00:00|大雨|フロリダ|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|00:08:00|竜巻|フロリダ|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|00:00:00|竜巻|アトランティック サウス|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|00:03:00|雷雨風|ミシシッピ|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|00:05:00|雷雨風|グルジア|

列名を再利用して、計算結果を同じ列に割り当てることができます。
次に例を示します。

```kusto
print x=1
| extend x = x + 1, y = x
| extend x = x + 1
```

|x|y|
|---|---|
|3|1|

[スカラー式](./scalar-data-types/index.md)には、通常のすべての演算子 (`+` `-`, `*` `/`, `%`, ) を含めることができ、さまざまな関数が役に立ちます。

## <a name="summarize-aggregate-groups-of-rows"></a>集計: 行のグループを集計する

各国から発生するイベント数をカウントします。

```kusto
StormEvents
| summarize event_count = count() by State
```

句内の同じ値を持つ行をまとめて集計し、集計関数 ( など[summarize](./summarizeoperator.md)`count`) を使用して各グループを 1 つの行にまとめます。 `by` したがって、この場合、各状態の行と、その状態の行数の列があります。

[集計関数](./summarizeoperator.md#list-of-aggregation-functions)にはさまざまな種類があり、1 つの集計演算子で複数の集計関数を使用して、複数の計算列を作成できます。 たとえば、各州の嵐の数と、州ごとの固有の種類の嵐の合計を取得できます。  
その後[、top](./topoperator.md)を使用して最も嵐の影響を受ける状態を取得できます。

```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

|State|ストームカウント|ストームの種類|
|---|---|---|
|テキサス州|4701|27|
|カンザス|3166|21|
|アイオワ州|2337|19|
|イリノイ|2022|23|
|ミズーリ|2016|20|

summarize の結果には以下のものが含まれます。

* `by`で指定された各列
* 計算された各式の列。
* `by` 値の組み合わせごとに 1 つの行

## <a name="summarize-by-scalar-values"></a>スカラー値による集計

この句ではスカラー (数値、時間、または間隔) の`by`値を使用できますが、値をビンに入れたいと思います。  
[bin()](./binfunction.md)関数は、次のような場合に便利です。

```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

これにより、すべてのタイムスタンプが 1 日の間隔に短縮されます。

|StartTime|event_count|
|---|---|
|2007-02-14 00:00:00.0000000|180|
|2007-02-15 00:00:00.0000000|66|
|2007-02-16 00:00:00.0000000|164|
|2007-02-17 00:00:00.0000000|103|
|2007-02-18 00:00:00.0000000|22|
|2007-02-19 00:00:00.0000000|52|
|2007-02-20 00:00:00.0000000|60|

[bin()](./binfunction.md)は多くの言語で[の floor()](./floorfunction.md)関数と同じです。 集計によってグループに行を割り[当てることができるように](./summarizeoperator.md)、すべての値を指定する係数の最も近い倍数に減らします。

## <a name="render-display-a-chart-or-table"></a>レンダリング: グラフまたはテーブルを表示する

2 つの列を投影し、グラフの x 軸と y 軸として使用します。

```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tour/060.png" alt-text="060":::

プロジェクトの運用`mid`を削除しましたが、チャートにその順序で国を表示する場合は、まだ必要です。

厳密に言うと、'render' はクエリ言語の一部ではなく、クライアントの機能です。 それでも、それは言語に統合されており、あなたの結果を想像するのに非常に便利です。


## <a name="timecharts"></a>時間グラフ

数値のビンに戻って、時系列を表示してみましょう:

```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tour/080.png" alt-text="080":::

## <a name="multiple-series"></a>複数の系列

値の組み合わせごとに別の行を作成する場合は、以下のように、 `summarize by` 句で複数の値を使用します。

```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tour/090.png" alt-text="090":::

上記にレンダリング用語を追加するだけです: `| render timechart`.

:::image type="content" source="images/tour/100.png" alt-text="100":::

最初の`render timechart`列を x 軸として使用し、その他の列を別々の行として表示します。

## <a name="daily-average-cycle"></a>日次平均サイクル

活動は平均的な日にどのように変化しますか?

ある日の剰余時間でイベントをカウントし、時間にビン分割します。 ビンの代わりに`floor`使用します。

```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tour/120.png" alt-text="120":::

現時点では`render`、期間に適切なラベルを付けることができませんが、`| render columnchart`代わりに使用できます。

:::image type="content" source="images/tour/110.png" alt-text="110":::

## <a name="compare-multiple-daily-series"></a>複数の日次系列の比較

さまざまな状態での時間帯の活動はどのように変化しますか?

```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

:::image type="content" source="images/tour/130.png" alt-text="130":::

X`1h`軸を期間ではなく時間数に変える場合に割ります。

```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tour/140.png" alt-text="140":::

## <a name="join"></a>join

2 つのイベントタイプについて、その両方がどのような状態で発生したのかを調べる方法はありますか。

最初の EventType と 2 番目の EventType を使用してストーム イベントをプルし、状態の 2 つのセットに参加できます。

```kusto
StormEvents
| where EventType == "Lightning"
| join (
    StormEvents 
    | where EventType == "Avalanche"
) on State  
| distinct State
```

:::image type="content" source="images/tour/145.png" alt-text="145":::

## <a name="user-session-example-of-join"></a>結合のユーザー セッションの例

このセクションでは、テーブルは`StormEvents`使用しません。

各セッションの開始と終了を示すイベントを含むデータがあり、各セッションの固有 ID があるとします。 

各ユーザー セッションの期間はどのくらいですか。

を使用`extend`して 2 つのタイムスタンプのエイリアスを指定すると、セッション期間を計算できます。

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

:::image type="content" source="images/tour/150.png" alt-text="150":::

結合を実行する前に必要な列のみを選択する場合は、 `project` を使用することをお勧めします。
その場合、同じ句で、タイムスタンプ列の名前を変更します。

## <a name="plot-a-distribution"></a>分布のプロット

いくつの嵐が異なる長さがありますか?

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

:::image type="content" source="images/tour/170.png" alt-text="170":::

または使用`| render columnchart`してください:

:::image type="content" source="images/tour/160.png" alt-text="160":::

## <a name="percentiles"></a>パーセンタイル

嵐の異なる割合をカバーする期間の範囲は何ですか?

上記のクエリを使用しますが`render`、次の値に置き換えます。

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

この場合、句を指定しなかった`by`ため、結果は単一の行になります。

:::image type="content" source="images/tour/180.png" alt-text="180":::

この結果から次のことがわかります。

* 嵐の5%の持続時間は5m未満です。
* 嵐の50%は1h 25m未満続く。
* 嵐の5%は少なくとも2h 50m続く。

各状態の内訳を個別に取得するには、両方の集計演算子を使用して状態列を個別に表示する必要があります。

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

:::image type="content" source="images/tour/190.png" alt-text="190":::

## <a name="let-assign-a-result-to-a-variable"></a>let: 結果を変数に代入する

let[を使用](./letstatement.md)して、上記の 'join' の例でクエリ式の部分を分離します。 結果は変わりません。

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

> ヒント: Kusto クライアントでは、この部分の間に空白行を入れないでください。 必ず、すべて間を空けずに実行してください。

## <a name="combining-data-from-several-databases-in-a-query"></a>クエリ内の複数のデータベースのデータの結合

詳細なディスカッション[については、クロスデータベース クエリ](./cross-cluster-or-database-queries.md)を参照してください。

スタイルのクエリを記述する場合:

```kusto
Logs | where ...
```

Logs という名前のテーブルは、デフォルトのデータベースに含める必要があります。 別のデータベースのテーブルにアクセスする場合は、次の構文を使用します。

```kusto
database("db").Table
```

したがって、診断と*テレメトリ*という名前*の*データベースがあり、そのデータの一部を関連付ける場合は、(診断がデフォルト*の*データベースであると仮定して) 書き込むかもしれません。

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

または、既定のデータベースが*テレメトリ*の場合

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
上記のすべては、両方のデータベースが現在接続しているクラスターに存在することを前提としています。 *テレメトリ*データベースが *、TelemetryCluster.kusto.windows.net*という名前の別のクラスターに属していて、その後、そのデータベースにアクセスする必要があるとします。

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> 注: クラスタが指定されている場合、データベースは必須です

::: zone-end

::: zone pivot="azuremonitor"

これは Azure モニターではサポートされていません。

::: zone-end