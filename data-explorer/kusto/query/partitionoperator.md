---
title: partition 演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの partition 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2b082e516a1118638bc8e61b545471326dd400e5
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346237"
---
# <a name="partition-operator"></a>partition 演算子

パーティション演算子は、指定された列の値に従って入力テーブルを複数のサブテーブルに分割し、各サブテーブルに対してサブクエリを実行し、すべてのサブクエリの結果の和集合である1つの出力テーブルを生成します。 

```kusto
T | partition by Col1 ( top 10 by MaxValue )

T | partition by Col1 { U | where Col2=toscalar(Col1) }
```

## <a name="syntax"></a>構文

*T* `|` `partition` [*partitionparameters*] `by` *列* `(` *ContextualSubquery*`)`

*T* `|` `partition` [*partitionparameters*] `by` *列*の `{` *サブクエリ*`}`

## <a name="arguments"></a>引数

* *T*: 演算子によって処理されるデータを含む表形式のソース。

* *列*: 入力テーブルのパーティション分割方法を決定する値を持つ、 *T*内の列の名前。 以下の**メモ**を参照してください。

* *ContextualSubquery*: テーブル式。 source は、 `partition` 1 つの*キー*値を対象とした、演算子のソースです。

* *サブクエリ*: ソースのない表形式の式です。 *キー*値は、call を使用して取得でき `toscalar()` ます。

* *Partitionparameters*: 0 個以上 (スペースで区切られた) パラメーターの形式で*Name* 、 `=` 演算子の動作を制御する名前*値*。 サポートされているパラメーターは次のとおりです。

  |名前               |値         |説明|
  |-------------------|---------------|-----------|
  |`hint.materialized`|`true`,`false` |に設定する `true` と、演算子のソースが具体化されます `partition` (既定値: `false` )。|
  |`hint.concurrency`|*Number*|システムに対して、演算子の同時実行サブクエリの数を並列で実行するかどうかを `partition` 指定します。 *既定値*: クラスターの単一ノードの CPU コアの量 (2 ~ 16)。|
  |`hint.spread`|*Number*|同時実行のサブクエリによって使用されるノードの数をシステムにヒントし `partition` ます。 *既定値*は1です。|

## <a name="returns"></a>戻り値

演算子は、サブクエリを入力データの各パーティションに適用した結果の和集合を返します。

**メモ**

* 現在、パーティション演算子は、パーティションの数によって制限されています。
  最大64の個別のパーティションを作成できます。
  パーティション列 (*列*) に64個を超える個別の値が含まれている場合、演算子はエラーを生成します。

* サブクエリは入力パーティションを暗黙的に参照します (サブクエリのパーティションに "name" はありません)。 サブクエリ内で入力パーティションを複数回参照するには、次のように[as 演算子](asoperator.md)を使用します (以下の**例: パーティション参照**)。

**例: 上位の入れ子になったケース**

場合によっては、演算子を使用する代わりに、演算子を使用してクエリを記述する方がパフォーマンスが高く、より簡単になり `partition` ます。次の例では、サブクエリの計算を実行し、 [ `top-nested` ](topnestedoperator.md) `summarize` `top` `W` : (ワイオミング州、ワシントン、WEST バージニア、ウィスコンシン) で始まる各州に対してを作成します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where State startswith 'W'
| partition by State 
(
    summarize Events=count(), Injuries=sum(InjuriesDirect) by EventType, State
    | top 3 by Events 
) 

```
|EventType|State|events|怪我|
|---|---|---|---|
|ひょう|ワイオミング州|108|0|
|高風|ワイオミング州|81|5|
|冬の嵐|ワイオミング州|72|0|
|重い雪|都|82|0|
|高風|都|58|13|
|なり|都|29|0|
|雷雨風|ウェストバージニア|180|1|
|ひょう|ウェストバージニア|103|0|
|冬の天候|ウェストバージニア|88|0|
|雷雨風|ウィスコンシン|416|1|
|冬の嵐|ウィスコンシン|310|0|
|ひょう|ウィスコンシン|303|1|

**例: 重複していないデータパーティションのクエリ**

Map/reduce スタイルで、重複していないデータパーティションに対して複雑なサブクエリを実行すると便利な場合があります (perf)。 次の例では、10個のパーティションに対して集計の手動分布を作成する方法を示します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| extend p = hash(EventId, 10)
| partition by p
(
    summarize Count=count() by Source 
)
| summarize Count=sum(Count) by Source
| top 5 by Count
```

|source|Count|
|---|---|
|訓練を受けた観測員|12770|
|法執行機関|8570|
|パブリック|6157|
|非常事態担当マネージャー|4900|
|CO-OP オブザーバー|3039|

**例: クエリ時間のパーティション分割**

次の例では、クエリを N = 10 個のパーティションにパーティション分割する方法を示します。各パーティションでは独自のカウントが計算され、後で TotalCount に集計されます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let N = 10;                 // Number of query-partitions
range p from 0 to N-1 step 1  // 
| partition by p            // Run the sub-query partitioned 
{
    StormEvents 
    | where hash(EventId, N) == toscalar(p) // Use toscalar() to fetch partition key value
    | summarize Count = count()
}
| summarize TotalCount=sum(Count) 
```

|TotalCount|
|---|
|59066|


**例: パーティション参照**

次の例では、 [as 演算子](asoperator.md)を使用して各データパーティションに "name" を指定し、サブクエリ内でその名前を再利用する方法を示しています。

```kusto
T
| partition by Dim
(
    as Partition
    | extend MetricPct = Metric * 100.0 / toscalar(Partition | summarize sum(Metric))
)
```

**例: 関数呼び出しによって非表示にされる複雑なサブクエリ**

同じ手法を、より複雑なサブクエリでも適用できます。 構文を簡略化するために、関数呼び出しでサブクエリをラップすることができます。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let partition_function = (T:(Source:string)) 
{
    T
    | summarize Count=count() by Source
};
StormEvents
| extend p = hash(EventId, 10)
| partition by p
(
    invoke partition_function()
)
| summarize Count=sum(Count) by Source
| top 5 by Count
```

|source|Count|
|---|---|
|訓練を受けた観測員|12770|
|法執行機関|8570|
|パブリック|6157|
|非常事態担当マネージャー|4900|
|CO-OP オブザーバー|3039|