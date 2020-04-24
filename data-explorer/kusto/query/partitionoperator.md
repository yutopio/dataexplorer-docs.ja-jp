---
title: パーティションオペレーター - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのパーティション演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0a9ef31f68a989fffe42d9b54800e9305e51f4ed
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511375"
---
# <a name="partition-operator"></a>partition 演算子

パーティション演算子は、指定された列の値に従って入力テーブルを複数のサブテーブルに分割し、各サブテーブルに対してサブクエリを実行し、すべてのサブクエリの結果の和集合である単一の出力テーブルを生成します。 

```kusto
T | partition by Col1 ( top 10 by MaxValue )

T | partition by Col1 { U | where Col2=toscalar(Col1) }
```

**構文**

*T* `|` T `partition` [*パーティションパラメータ ]* `by` *列*`(`*コンテキストサブクエリ*`)`

*T* `|` T `partition` [*パーティションパラメータ ]* `by` *列*`{`*サブクエリ*`}`

**引数**

* *T*: データを処理するテーブル ソース。

* *列*: T*内の*列の名前で、その値によって入力テーブルのパーティション分割方法が決まります。 以下**の「注」** を参照してください。

* *コンテキストサブクエリ*: 演算子のソースである表形式の式で`partition`、単一の*キー*値のスコープを指定します。

* *サブクエリ*: ソースのない表形式の式。 *キー*値は、呼び出`toscalar()`しを介して取得できます。

* *PartitionParameters*: 0 個以上 (スペースで区切られた) パラメーター:*演算子*`=`の動作を制御する*Name 値*。 サポートされているパラメーターは次のとおりです。

  |名前               |値         |説明|
  |-------------------|---------------|-----------|
  |`hint.materialized`|`true`,`false` |に`true`設定すると、オペレータのソースが具体化`partition`されます (デフォルト`false`: )|
  |`hint.concurrency`|*数*|並列実行する演算子の同時サブクエリの数を`partition`システムに示します。 *デフォルト*: クラスタの単一ノード上の CPU コアの量 (2 ~ 16)。|
  |`hint.spread`|*数*|同時`partition`実行サブクエリで使用するノード数をシステムに示します。 *デフォルト*: 1.|

**戻り値**

演算子は、入力データの各パーティションにサブクエリを適用した結果の和集合を返します。

**メモ**

* パーティション演算子は、現在パーティションの数によって制限されています。
  最大 64 のパーティションを作成できます。
  パーティション列 (*Column*) に 64 個を超える個別値が含まれている場合、演算子はエラーになります。

* サブクエリは入力パーティションを暗黙的に参照します (サブクエリ内にパーティションの "name" はありません)。 サブクエリ内で入力パーティションを複数回参照するには、以下の**例: パーティション参照**のように[as 演算子](asoperator.md)を使用します。

**例: トップネストのケース**

場合によっては、演算子を使用するのではなく`partition`、[`top-nested`演算子](topnestedoperator.md)を使用してクエリを記述する方がパフォーマンスが高く、クエリを記述しやすくなる次`summarize`の`top`例では、サブクエリ計算を`W`実行し、次の例で始まる各州を実行します(WYOMING、ワシントン州、ウェストバージニア州、ウィスコンシン州)

```kusto
StormEvents
| where State startswith 'W'
| partition by State 
(
    summarize Events=count(), Injuries=sum(InjuriesDirect) by EventType, State
    | top 3 by Events 
) 

```
|EventType|State|イベント|傷害|
|---|---|---|---|
|ひょう|ワイオミング|108|0|
|強風|ワイオミング|81|5|
|冬の嵐|ワイオミング|72|0|
|大雪|ワシントン|82|0|
|強風|ワシントン|58|13|
|野火|ワシントン|29|0|
|雷雨風|ウェストバージニア|180|1|
|ひょう|ウェストバージニア|103|0|
|冬の天気|ウェストバージニア|88|0|
|雷雨風|ウィスコンシン|416|1|
|冬の嵐|ウィスコンシン|310|0|
|ひょう|ウィスコンシン|303|1|

**例: 重複しないデータ・パーティションの照会**

マップ/reduce スタイルで重複しないデータ パーティションに対して複雑なサブクエリを実行すると便利な場合があります (perf 的に)。 次の例は、10 パーティションにわたる集計の手動配布を作成する方法を示しています。

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
|コープオブザーバー|3039|

**例: クエリ時パーティション分割**

次の例では、クエリを N=10 パーティションに分割し、各パーティションが独自の Count を計算し、後で TotalCount に集計する方法を示します。

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

次の例は[、as 演算子](asoperator.md)を使用して各データ パーティションに "name" を指定し、サブクエリ内でその名前を再利用する方法を示しています。

```kusto
T
| partition by Dim
(
    as Partition
    | extend MetricPct = Metric * 100.0 / toscalar(Partition | summarize sum(Metric))
)
```

**例: 関数呼び出しによって隠された複合サブクエリ**

同じ手法は、はるかに複雑なサブクエリでも適用できます。 構文を簡略化するために、関数呼び出しでサブクエリをラップできます。

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
|コープオブザーバー|3039|