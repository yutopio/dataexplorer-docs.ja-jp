---
title: シャッフルクエリ-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのシャッフルクエリについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d3625be5a3a97b456a2d6d84802b11602f959f3e
ms.sourcegitcommit: bb7c2ba9f9dcae08710be2345ee6e63004629ea1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88218981"
---
# <a name="shuffle-query"></a>クエリのシャッフル

シャッフルクエリは、シャッフル戦略をサポートする一連の演算子に対するセマンティック保持変換です。 実際のデータによっては、このクエリのパフォーマンスが大幅に向上する可能性があります。

Kusto でシャッフルをサポートする演算子は、 [結合](joinoperator.md)、 [集計](summarizeoperator.md)、および [シリーズ](make-seriesoperator.md)です。

クエリパラメーターまたはを使用してシャッフルクエリ方法を設定し `hint.strategy = shuffle` `hint.shufflekey = <key>` ます。

## <a name="syntax"></a>構文

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.strategy = shuffle (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

```kusto
T
| summarize hint.strategy = shuffle count(), avg(price) by supplier
```

```kusto
T
| make-series hint.shufflekey = Fruit PriceAvg=avg(Price) default=0  on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

この方法では、各ノードがデータの1つのパーティションを処理するすべてのクラスターノードで負荷が共有されます。
キー ( `join` キー、 `summarize` キー、または `make-series` キー) のカーディナリティが高く、通常のクエリ方法でクエリの制限に達した場合は、シャッフルクエリ戦略を使用すると便利です。

**ヒントの相違点。方法 = シャッフルとヒント. shufflekey = キー**

`hint.strategy=shuffle` は、シャッフルされた演算子がすべてのキーでシャッフルされることを意味します。
たとえば、次のクエリでは次のようになります。

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.strategy = shuffle (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

データをシャッフルするハッシュ関数は、ActivityId と ProcessId の両方のキーを使用します。

上記のクエリは、次の場合と同じです。

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.shufflekey = ActivityId hint.shufflekey = ProcessId (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

複合キーが一意ではなく、各キーが一意ではない場合は、これを使用して、シャッフルされた `hint` 演算子のすべてのキーでデータをシャッフルします。
シャッフルされた演算子にやなどのシャッフル可能な他の演算子がある場合、 `summarize` `join` クエリはより複雑になり、ヒントになります。方法 = シャッフルは適用されません。

次に例を示します。

```kusto
T
| where Event=="Start"
| project ActivityId, Started=Timestamp, numeric_column
| summarize count(), numeric_column = any(numeric_column) by ActivityId
| join
    hint.strategy = shuffle (T
    | where Event=="End"
    | project ActivityId, Ended=Timestamp, numeric_column
)
on ActivityId, numeric_column
| extend Duration=Ended - Started
| summarize avg(Duration)
```

`hint.strategy=shuffle`(クエリの計画時に戦略を無視するのではなく) を適用し、複合キー [,] によってデータをシャッフルすると、 `ActivityId` `numeric_column` 結果は正しくありません。
演算子は、 `summarize` 演算子の左側に `join` あります。 この演算子は、キーのサブセットによってグループ化され `join` ます。ここでは、 `ActivityId` です。 したがって、は `summarize` キーによってグループ化され、 `ActivityId` データは複合キー [,] によってパーティション分割され `ActivityId` `numeric_column` ます。
複合キー [,] によるシャッフル `ActivityId` `numeric_column` は、必ずしもキーのシャッフルが有効であることを意味するわけでは `ActivityId` なく、結果が正しくない可能性があります。

次の例では、複合キーに使用されるハッシュ関数がであることを前提としてい `binary_xor(hash(key1, 100) , hash(key2, 100))` ます。

```kusto

datatable(ActivityId:string, NumericColumn:long)
[
"activity1", 2,
"activity1" ,1,
]
| extend hash_by_key = binary_xor(hash(ActivityId, 100) , hash(NumericColumn, 100))
```

|ActivityId|NumericColumn|hash_by_key|
|---|---|---|
|activity1|2|56|
|activity1|1|65|

両方のレコードの複合キーが異なるパーティション (56 および 65) にマップされましたが、これらの2つのレコードの値は同じ `ActivityId` です。 の左側の演算子では、 `summarize` `join` 同じパーティションに列の類似した値が使用 `ActivityId` されます。 このクエリでは、正しくない結果が生成されます。

この問題を解決するには、を使用して `hint.shufflekey` 、への結合のシャッフルキーを指定し `hint.shufflekey = ActivityId` ます。 このキーは、すべてのシャッフル可能なオペレーターに共通です。
とは両方 `join` とも `summarize` 同じキーでシャッフルされるため、この場合、シャッフルは安全です。 これにより、類似したすべての値が同じパーティションに配置され、結果は正しいことになります。

```kusto
T
| where Event=="Start"
| project ActivityId, Started=Timestamp, numeric_column
| summarize count(), numeric_column = any(numeric_column) by ActivityId
| join
    hint.shufflekey = ActivityId (T
    | where Event=="End"
    | project ActivityId, Ended=Timestamp, numeric_column
)
on ActivityId, numeric_column
| extend Duration=Ended - Started
| summarize avg(Duration)
```

|ActivityId|NumericColumn|hash_by_key|
|---|---|---|
|activity1|2|56|
|activity1|1|65|

シャッフルクエリでは、既定のパーティション番号がクラスターノード数になります。 この数値は、パーティションの数を制御する構文を使用してオーバーライドでき `hint.num_partitions = total_partitions` ます。

このヒントは、クラスターに少数のクラスターノードがあり、既定のパーティション番号が小さくなり、クエリが引き続き失敗するか、実行時間が長い場合に便利です。

> [!Note]
> 多くのパーティションがあると、より多くのクラスターリソースを消費し、パフォーマンスが低下する可能性があります。 代わりに、ヒントで始まるパーティション番号を慎重に選択してください。方法 = シャッフルし、パーティションを徐々に増やし始めます。

## <a name="examples"></a>例

次の例は、シャッフルによってパフォーマンスが大幅に向上する方法を示して `summarize` います。

ソーステーブルには、150 m レコードがあり、group by キーのカーディナリティは10万で、10個のクラスターノードに分散されています。

通常の `summarize` 方法を実行すると、クエリは1:08 より後に終了し、メモリ使用量のピークは約 3 GB になります。

```kusto
orders
| summarize arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Count|
|---|
|1086|

シャッフル戦略を使用している間 `summarize` 、クエリは最大7秒後に終了し、メモリ使用量は 0.43 GB になります。

```kusto
orders
| summarize hint.strategy = shuffle arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Count|
|---|
|1086|

次の例では、2つのクラスターノードがあり、テーブルに60M レコードがあり、group by キーのカーディナリティが2M であるクラスターの改善点を示しています。

を指定せずにクエリを実行する `hint.num_partitions` と、(クラスターノード数として) 2 つのパーティションのみが使用され、次のクエリは約1:10 分かかります。

```kusto
lineitem    
| summarize hint.strategy = shuffle dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

パーティション数を10に設定すると、クエリは23秒後に終了します。 

```kusto
lineitem    
| summarize hint.strategy = shuffle hint.num_partitions = 10 dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

次の例は、シャッフルによってパフォーマンスが大幅に向上する方法を示して `join` います。

これらの例は、ノードが10個あるクラスターでサンプリングされており、これらのノードのすべてにデータが分散しています。

左テーブルには、キーのカーディナリティが ~ 14M である15,000,000 回レコードがあり `join` ます。 の右側には、 `join` 150 m レコードがあり、キーのカーディナリティは10万 `join` です。
の通常の戦略を実行 `join` すると、クエリは約28秒後に終了し、メモリ使用量のピークは 1.43 GB になります。

```kusto
customer
| join
    orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

シャッフル戦略を使用している間 `join` 、クエリは最大4秒後に終了し、メモリ使用量は 0.3 GB になります。

```kusto
customer
| join
    hint.strategy = shuffle orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

の左側 `join` が 150 m で、キーのカーディナリティが148M である、より大きなデータセットに対して同じクエリを実行しようとしています。 の右側は `join` 1.5 b で、キーのカーディナリティは ~ 100m です。

既定の戦略を使用したクエリは、 `join` 4 分後に Kusto の制限とタイムアウトに達します。
シャッフル戦略を使用している間 `join` 、クエリは約34秒後に終了し、メモリ使用量は 1.23 GB になります。


次の例では、2つのクラスターノードがあり、テーブルに60M レコードがあり、キーのカーディナリティが2M であるクラスターの改善点を示してい `join` ます。
を指定せずにクエリを実行する `hint.num_partitions` と、(クラスターノード数として) 2 つのパーティションのみが使用され、次のクエリは約1:10 分かかります。

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey   part
on $left.l_partkey == $right.p_partkey
| consume
```

パーティション数を10に設定すると、クエリは23秒後に終了します。 

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey  hint.num_partitions = 10    part
on $left.l_partkey == $right.p_partkey
| consume
```
