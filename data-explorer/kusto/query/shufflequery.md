---
title: シャッフル クエリ - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのシャッフル クエリについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c687d495a41a5f73ac8dbca15d93729f2132a556
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81662971"
---
# <a name="shuffle-query"></a>シャッフル クエリ

シャッフル クエリは、実際のデータに応じてパフォーマンスが大幅に向上するシャッフル戦略をサポートする一連の演算子のセマンティックを保持する変換です。

Kusto でのシャッフルをサポートするオペレーターは、[結合](joinoperator.md)、[要約](summarizeoperator.md)、[およびメイクシリーズ です](make-seriesoperator.md)。

シャッフル クエリストラテジーは、クエリ`hint.strategy = shuffle`パラメータ`hint.shufflekey = <key>`または .

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

この戦略は、各ノードがデータの 1 つのパーティションを処理するすべてのクラスター ノードで負荷を共有します。
キー (`join`キー、キー、キー)`summarize``make-series`の基数が高い場合は、通常のクエリ戦略がクエリの制限に達する場合は、シャッフル クエリ戦略を使用すると便利です。

**ヒント戦略=シャッフルとヒント.シャッフルキーの違い = キー**

`hint.strategy=shuffle`は、シャッフルされたオペレータがすべてのキーでシャッフルされることを意味します。
たとえば、次のクエリを使用します。

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.strategy = shuffle (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

データをシャッフルするハッシュ関数は、ActivityId キーと ProcessId キーの両方を使用します。

上記のクエリは、 と同じです。

```kusto
T | where Event=="Start" | project ActivityId, Started=Timestamp
| join hint.shufflekey = ActivityId hint.shufflekey = ProcessId (T | where Event=="End" | project ActivityId, Ended=Timestamp)
  on ActivityId, ProcessId
| extend Duration=Ended - Started
| summarize avg(Duration)
```

このヒントは、複合キーが一意ではありますが、各キーが十分に一意ではないため、シャッフルされたオペレータのすべてのキーでデータをシャッフルする場合に使用できます。
シャッフルされた演算子に、 または`summarize``join`のような他のシャッフル可能な演算子がある場合、クエリはより複雑になり、hint.strategy= shuffle は適用されません。

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

この場合`hint.strategy=shuffle`、(クエリ計画中にストラテジーを無視するのではなく)を適用し、複合キー [`ActivityId`]`numeric_column`でデータをシャッフルすると、結果は正しくなりません。
は`summarize`、キーのサブセットによって`join`グルーブの`join`左側にあります。 `ActivityId` これは、データが`summarize`複合キー [ `ActivityId` `ActivityId`, `numeric_column`] で分割されている間に、キーによってグループ化されることを意味します。
複合キー [`ActivityId`, `numeric_column`] でシャッフルすることは、それが Key ActivityId の有効なシャッフルであり、結果が正しくない可能性があることを意味しません。

この例では、複合キーに使用されるハッシュ関数が`binary_xor(hash(key1, 100) , hash(key2, 100))`

```kusto

datatable(ActivityId:string, NumericColumn:long)
[
"activity1", 2,
"activity1" ,1,
]
| extend hash_by_key = binary_xor(hash(ActivityId, 100) , hash(NumericColumn, 100))
```

|ActivityId|数値列|hash_by_key|
|---|---|---|
|アクティビティ1|2|56|
|アクティビティ1|1|65|



両方のレコードの複合キーが異なるパーティション 56 と 65 にマップされているのを見`ActivityId`てわかるように`summarize`、これらの 2 つのレコードの値は同`join`じで、同じパーティション内`ActivityId`に列の類似値が存在すると想定される左側の値は、間違った結果を生成します。

この場合、`hint.shufflekey`すべてのシャッフル可能な演算子の共通キー`hint.shufflekey = ActivityId`である結合にシャッフル キーを指定することで、この問題を解決します。
この場合、シャッフルは安全で、両方とも`join`同じキー`summarize`でシャッフルするので、すべての類似した値が同じパーティションに間違いなく結果が正しいようにします。

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

|ActivityId|数値列|hash_by_key|
|---|---|---|
|アクティビティ1|2|56|
|アクティビティ1|1|65|

シャッフル クエリでは、デフォルトのパーティション番号はクラスター ノード番号です。 この数は、パーティションの数を制御する`hint.num_partitions = total_partitions`構文を使用して上書きできます。

このヒントは、クラスターに含まれるクラスター ノードの数が少なくて、デフォルトのパーティション番号も小さくなり、クエリが失敗したり実行に時間がかかる場合に役立ちます。

多数のパーティションを設定すると、パフォーマンスが低下し、より多くのクラスタ リソースが消費される可能性があるため、パーティション番号を慎重に選択することをお勧めします (hint.strategy = シャッフルから開始し、パーティションを徐々に増やしてください)。

**使用例**

次の例は、シャッフル`summarize`によってパフォーマンスが大幅に向上する方法を示しています。

ソーステーブルには150Mのレコードがあり、キーによるグループのカーディナリティは10Mで、10のクラスタノードに分散されます。

通常`summarize`のストラテジーを実行すると、クエリは 1:08 以降終了し、メモリ使用量のピークは約 3 GB になります。

```kusto
orders
| summarize arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Count|
|---|
|1086|

シャッフル`summarize`ストラテジを使用している間、クエリは約7秒後に終了し、メモリ使用量のピークは0.43GBです。

```kusto
orders
| summarize hint.strategy = shuffle arg_max(o_orderdate, o_totalprice) by o_custkey 
| where o_totalprice < 1000
| count
```

|Count|
|---|
|1086|

次の例は、2 つのクラスター・ノードを持つクラスターの改善を示し、表には 60M レコードがあり、グループのカーディナリティーは 2M です。

クエリを実行せずに`hint.num_partitions`(クラスタ ノード番号として) 2 つのパーティションのみを使用し、次のクエリには約 1:10 分かかります。

```kusto
lineitem    
| summarize hint.strategy = shuffle dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

パーティション番号を 10 に設定すると、クエリは 23 秒後に終了します。 

```kusto
lineitem    
| summarize hint.strategy = shuffle hint.num_partitions = 10 dcount(l_comment), dcount(l_shipdate) by l_partkey 
| consume
```

次の例は、シャッフル`join`によってパフォーマンスが大幅に向上する方法を示しています。

この例は、データがこれらすべてのノードに分散される 10 ノードのクラスターでサンプリングされました。

左のテーブルには、`join`キーのカーディナリティーが~14M、右側`join`が150Mレコード、キーのカーディナリティが10Mである15Mレコードが`join`含まれます。
の通常の戦略を`join`実行すると、クエリは約 28 秒後に終了し、メモリ使用量のピークは 1.43 GB になります。

```kusto
customer
| join
    orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

シャッフル`join`ストラテジーを使用している間、クエリは約4秒後に終了し、メモリ使用量のピークは0.3GBになります。

```kusto
customer
| join
    hint.strategy = shuffle orders
on $left.c_custkey == $right.o_custkey
| summarize sum(c_acctbal) by c_nationkey
```

より大きなデータセットで同じクエリを実行すると`join`、左辺が 150M、キーのカーディナリティーが 148M、右側`join`が 1.5B、キーのカーディナリティが約 100M になります。

既定`join`の戦略を使用したクエリは、4 分後に kusto の制限とタイムアウトに達します。
シャッフル`join`方式を使用している間、クエリは約 34 秒後に終了し、メモリ使用量のピークは 1.23 GB です。


次の例は、2 つのクラスターノードを持つクラスターでの改善を示し、表には 60M`join`レコードがあり、キーのカーディナリティーは 2M です。
クエリを実行せずに`hint.num_partitions`(クラスタ ノード番号として) 2 つのパーティションのみを使用し、次のクエリには約 1:10 分かかります。

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey   part
on $left.l_partkey == $right.p_partkey
| consume
```

パーティション番号を 10 に設定すると、クエリは 23 秒後に終了します。 

```kusto
lineitem
| summarize dcount(l_comment), dcount(l_shipdate) by l_partkey
| join
    hint.shufflekey = l_partkey  hint.num_partitions = 10    part
on $left.l_partkey == $right.p_partkey
| consume
```