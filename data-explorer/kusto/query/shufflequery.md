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
ms.openlocfilehash: bbae74ccdf0d9840a6ba4aa7427155f89c4af211
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82742013"
---
# <a name="shuffle-query"></a>クエリのシャッフル

シャッフルクエリは、シャッフル戦略をサポートする一連の演算子に対するセマンティック保持変換です。 実際のデータによっては、このクエリのパフォーマンスが大幅に向上する可能性があります。

Kusto でシャッフルをサポートする演算子は、[結合](joinoperator.md)、[集計](summarizeoperator.md)、および[シリーズ](make-seriesoperator.md)です。

クエリパラメーター `hint.strategy = shuffle`または`hint.shufflekey = <key>`を使用してシャッフルクエリ方法を設定します。

テーブルで[データのパーティション分割ポリシー](../management/partitioningpolicy.md)を定義します。 

クラスター `shufflekey`ノード間を移動するために必要なデータの量が少なくなるため、パフォーマンスを向上させるためにテーブルのハッシュパーティションキーとして設定します。

**構文**

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
キー (`join`キー、 `summarize`キー、または`make-series`キー) のカーディナリティが高く、通常のクエリ方法でクエリの制限に達した場合は、シャッフルクエリ戦略を使用すると便利です。

**ヒントの相違点。方法 = シャッフルとヒント. shufflekey = キー**

`hint.strategy=shuffle`は、シャッフルされた演算子がすべてのキーでシャッフルされることを意味します。
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

複合キーが一意ではなく、各キーが一意ではない場合は、 `hint`これを使用して、シャッフルされた演算子のすべてのキーでデータをシャッフルします。
シャッフルされた演算子に`summarize`や`join`などのシャッフル可能な他の演算子がある場合、クエリはより複雑になり、ヒントになります。方法 = シャッフルは適用されません。

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

`hint.strategy=shuffle` (クエリの計画時に戦略を無視するのではなく) を適用し、複合キー [`ActivityId`, `numeric_column`] によってデータをシャッフルすると、結果は正しくありません。
`summarize`演算子は、 `join`演算子の左側にあります。 この演算子は、 `join`キーのサブセットによってグループ化されます`ActivityId`。ここでは、です。 したがって、 `summarize`はキー `ActivityId`によってグループ化され、データは複合キー [`ActivityId`, `numeric_column`] によってパーティション分割されます。
複合キー [`ActivityId`, `numeric_column`] によるシャッフルは、必ずしもキー `ActivityId`のシャッフルが有効であることを意味するわけではなく、結果が正しくない可能性があります。

次の例では、複合キーに使用されるハッシュ`binary_xor(hash(key1, 100) , hash(key2, 100))`関数がであることを前提としています。

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

両方のレコードの複合キーが異なるパーティション (56 および 65) にマップされましたが、これらの2つ`ActivityId`のレコードの値は同じです。 の`summarize` `join`左側の演算子では、同じパーティションに列`ActivityId`の類似した値が使用されます。 このクエリでは、正しくない結果が生成されます。

この問題を解決するには`hint.shufflekey` 、を使用して、へ`hint.shufflekey = ActivityId`の結合のシャッフルキーを指定します。 このキーは、すべてのシャッフル可能なオペレーターに共通です。
とは両方`join`と`summarize`も同じキーでシャッフルされるため、この場合、シャッフルは安全です。 これにより、類似したすべての値が同じパーティションに配置され、結果は正しいことになります。

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

シャッフルクエリでは、既定のパーティション番号がクラスターノード数になります。 この数値は、パーティションの数を制御`hint.num_partitions = total_partitions`する構文を使用してオーバーライドできます。

このヒントは、クラスターに少数のクラスターノードがあり、既定のパーティション番号が小さくなり、クエリが引き続き失敗するか、実行時間が長い場合に便利です。

> [!Note]
> 多くのパーティションがあると、より多くのクラスターリソースを消費し、パフォーマンスが低下する可能性があります。 代わりに、ヒントで始まるパーティション番号を慎重に選択してください。方法 = シャッフルし、パーティションを徐々に増やし始めます。

**使用例**

次の例は、シャッフル`summarize`によってパフォーマンスが大幅に向上する方法を示しています。

ソーステーブルには、150 m レコードがあり、group by キーのカーディナリティは10万で、10個のクラスターノードに分散されています。

通常`summarize`の方法を実行すると、クエリは1:08 より後に終了し、メモリ使用量のピークは約 3 GB になります。

```kusto
orders
| summarize arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Count|
|---|
|1086|

シャッフル`summarize`戦略を使用している間、クエリは最大7秒後に終了し、メモリ使用量は 0.43 GB になります。

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

を指定せず`hint.num_partitions`にクエリを実行すると、(クラスターノード数として) 2 つのパーティションのみが使用され、次のクエリは約1:10 分かかります。

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

次の例は、シャッフル`join`によってパフォーマンスが大幅に向上する方法を示しています。

これらの例は、ノードが10個あるクラスターでサンプリングされており、これらのノードのすべてにデータが分散しています。

左テーブルには、 `join`キーのカーディナリティが ~ 14m である15,000,000 回レコードがあります。 の右側に`join`は、150 m レコードがあり、 `join`キーのカーディナリティは10万です。
の通常の戦略を実行`join`すると、クエリは約28秒後に終了し、メモリ使用量のピークは 1.43 GB になります。

```kusto
customer
| join
    orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

シャッフル`join`戦略を使用している間、クエリは最大4秒後に終了し、メモリ使用量は 0.3 GB になります。

```kusto
customer
| join
    hint.strategy = shuffle orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

の左側`join`が 150 m で、キーのカーディナリティが148M である、より大きなデータセットに対して同じクエリを実行しようとしています。 の右側`join`は 1.5 b で、キーのカーディナリティは ~ 100m です。

既定`join`の戦略を使用したクエリは、4分後に Kusto の制限とタイムアウトに達します。
シャッフル`join`戦略を使用している間、クエリは約34秒後に終了し、メモリ使用量は 1.23 GB になります。


次の例では、2つのクラスターノードがあり、テーブルに60M レコードがあり、 `join`キーのカーディナリティが2m であるクラスターの改善点を示しています。
を指定せず`hint.num_partitions`にクエリを実行すると、(クラスターノード数として) 2 つのパーティションのみが使用され、次のクエリは約1:10 分かかります。

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
