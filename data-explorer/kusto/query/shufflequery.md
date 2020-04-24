---
title: シャッフルクエリ-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのシャッフルクエリについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 600e561937b779ff9dd10d5d82f5522d204466a0
ms.sourcegitcommit: 2e63c7c668c8a6200f99f18e39c3677fcba01453
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117694"
---
# <a name="shuffle-query"></a>クエリのシャッフル

シャッフルクエリは、実際のデータによってはパフォーマンスを大幅に向上させることができる、シャッフル戦略をサポートする一連のオペレーターに対するセマンティック保持変換です。

Kusto でシャッフルをサポートする演算子は、[結合](joinoperator.md)、[集計](summarizeoperator.md)、および[系列](make-seriesoperator.md)です。

シャッフルクエリ戦略は、クエリパラメーター `hint.strategy = shuffle`または`hint.shufflekey = <key>`を使用して設定できます。

テーブルに[データパーティション分割ポリシー](../management/partitioningpolicy.md)を定義する方法についても説明します。 `shufflekey`がテーブルのハッシュパーティションキーでもあるクエリは、クラスターノード間の移動に必要なデータの量が大幅に削減されるため、パフォーマンスが向上します。

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

この戦略では、各ノードがデータの1つのパーティションを処理するすべてのクラスターノードの負荷を共有します。
キー (`join`キー、キー、 `summarize`または`make-series`キー) に大きなカーディナリティがあり、通常のクエリ方法によってクエリの制限がヒットする場合は、シャッフルクエリ戦略を使用すると便利です。

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

このヒントは、複合キーが一意であるにもかかわらず、各キーが一意ではないため、シャッフルされた演算子のすべてのキーによってデータをシャッフルする場合に使用できます。
シャッフルされた演算子に`summarize`や`join`などの他の shufflable 演算子がある場合、クエリはより複雑になり、ヒントになります。方法 = シャッフルは適用されません。

例えば：

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

この場合、 `hint.strategy=shuffle` (クエリの計画時に戦略を無視するのではなく) を適用し、複合キー [`ActivityId`, `numeric_column`] によってデータをシャッフルすると、結果は正しくありません。
`summarize` `join`が`ActivityId`のキーのサブセットによって`join` groubs の左側にある。 つまり、は、 `summarize`複合キー [`ActivityId`, `numeric_column`] `ActivityId`によってデータがパーティション分割されている間に、キーによってグループ化されることを意味します。
複合キー [`ActivityId`, `numeric_column`] によるシャッフルは、それがキー ActivityId の有効なシャッフルであるという意味ではなく、結果が正しくない可能性があります。

次の例では、複合キーに使用されるハッシュ関数がであることを前提としています。`binary_xor(hash(key1, 100) , hash(key2, 100))`

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



両方のレコードの複合キーが異なるパーティション56と65にマップされていますが、これらの2つの`ActivityId`レコードの値が`summarize`同じである場合、の`join`左側のは同じパーティションにある`ActivityId`列の類似した値を期待しているので、defintely によって誤った結果が生成されます。

この場合、は`hint.shufflekey` 、結合`hint.shufflekey = ActivityId`のシャッフルキーを指定することによって、この問題を解決します。これは、すべての shuffelable 演算子の共通キーです。
この場合、シャッフルは安全であり、両方`join`と`summarize`も同じキーによってシャッフルされるため、類似したすべての値が同じパーティションに defintely されるため、結果は正しいものになります。

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

多くのパーティションを設定すると、パフォーマンスが低下し、より多くのクラスターリソースを消費する可能性があるので、パーティション番号を慎重に選択することをお勧めします (ヒントから始めて、パーティションを徐々に増やし始めます)。

**例**

次の例は、シャッフル`summarize`によってパフォーマンスが大幅に向上する方法を示しています。

ソーステーブルには、150 m レコードがあり、group by キーのカーディナリティは10個のクラスターノードに分散されています。

通常`summarize`の方法を実行すると、クエリは1:08 より後に終了し、メモリ使用量は最大 3 gb になります。

```kusto
orders
| summarize arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Count|
|---|
|1086|

シャッフル`summarize`戦略を使用している間、クエリは最大7秒後に終了し、メモリ使用量は 0.43 gb になります。

```kusto
orders
| summarize hint.strategy = shuffle arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Count|
|---|
|1086|

次の例は、2つのクラスターノードを持つクラスターでの改善点を示しています。テーブルには60M レコードがあり、group by キーのカーディナリティは2M です。

を指定せず`hint.num_partitions`にクエリを実行すると、2つのパーティション (クラスターノードの数) のみが使用され、次のクエリは約1:10 分かかります。

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

左テーブルには、 `join`キーのカーディナリティが ~ 14m である15,000,000 回レコードがあります`join` 。の右側には、150 m レコードが`join`あり、キーのカーディナリティは10万です。
の通常の戦略を実行`join`すると、クエリは最大28秒後に終了し、メモリ使用量は 1.43 gb になります。

```kusto
customer
| join
    orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

シャッフル`join`戦略を使用している間、クエリは約4秒後に終了し、メモリ使用量のピークは 0.3 gb になります。

```kusto
customer
| join
    hint.strategy = shuffle orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

大規模なデータセットに対して同じクエリを実行し`join`ようとしたときに、の左側が 150 m で、キーの`join`カーディナリティが148M である場合、の右側は 1.5 b で、キーのカーディナリティは約100m です。

既定`join`の戦略を使用したクエリは、4分後に kusto の制限とタイムアウトに達します。
シャッフル`join`戦略を使用している間、クエリは約34秒後に終了し、メモリ使用量は 1.23 gb になります。


次の例は、2つのクラスターノードを持つクラスターでの改善点を示しています。テーブルには`join` 60m レコードがあり、キーのカーディナリティは2m です。
を指定せず`hint.num_partitions`にクエリを実行すると、2つのパーティション (クラスターノードの数) のみが使用され、次のクエリは約1:10 分かかります。

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
