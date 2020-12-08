---
title: join 演算子 - Azure Data Explorer
description: この記事では、Azure Data Explorer の join 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b90e5f1c95ec75a946490cd75b5dd89ad2cb1aba
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513337"
---
# <a name="join-operator"></a>join 演算子

各テーブルの指定した列の値を照合することにより、2 つのテーブルの行をマージして新しいテーブルを作成します。

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

## <a name="syntax"></a>構文

*LeftTable* `|` `join` [*JoinParameters*] `(` *RightTable* `)` `on` *Attributes*

## <a name="arguments"></a>引数

* *LeftTable*: 行をマージする **左側** のテーブルまたは表形式の式。**外部** テーブルとも呼ばれます。 `$left` と示されます。

* *RightTable*: 行をマージする **右側** のテーブルまたは表形式の式。**内部** テーブルとも呼ばれます。 `$right` と示されます。

* *Attributes*: *LeftTable* の行と *RightTable* の行を照合する方法が記述されている、1 つまたは複数のコンマで区切られた **ルール**。 複数のルールは、`and` 論理演算子を使用して評価されます。

  **ルール** には次のいずれかを指定できます。

  |ルールの種類        |構文          |Predicate    |
  |-----------------|--------------|-------------------------|
  |名前による等値性 |*[ColumnName]*    |`where` *LeftTable*.*ColumnName* `==` *RightTable*.*ColumnName*|
  |値による等値性|`$left.`*LeftColumn* `==` `$right.`*RightColumn*|`where` `$left.`*LeftColumn* `==` `$right.`*RightColumn*       |

    > [!NOTE]
    > "値による等値性" の場合、`$left` および `$right` の表記によって示される該当する所有者テーブルで列名を修飾する *必要があります*。

* *JoinParameters*: 行の照合操作と実行プランの動作を制御する、空白で区切られた "*名前* `=` *値*" の形式の 0 個以上のパラメーター。 サポートされているパラメーターは次のとおりです。

    ::: zone pivot="azuredataexplorer"

    |パラメーター名           |値                                        |[説明]                                  |
    |---------------|----------------------------------------------|---------------------------------------------|
    |`kind`         |結合の種類|「[結合のフレーバー](#join-flavors)」を参照してください|                                             |
    |`hint.remote`  |`auto`, `left`, `local`, `right`              |「[クラスター間の結合](joincrosscluster.md)」を参照してください|
    |`hint.strategy`|実行のヒント                               |「[結合のヒント](#join-hints)」を参照してください                |

    ::: zone-end

    ::: zone pivot="azuremonitor"

    |名前           |値                                        |[説明]                                  |
    |---------------|----------------------------------------------|---------------------------------------------|
    |`kind`         |結合の種類|「[結合のフレーバー](#join-flavors)」を参照してください|                                             |
    |`hint.remote`  |`auto`, `left`, `local`, `right`              |                                             |
    |`hint.strategy`|実行のヒント                               |「[結合のヒント](#join-hints)」を参照してください                |

    ::: zone-end

> [!WARNING]
> `kind` を指定しない場合の既定の結合のフレーバーは `innerunique` です。 これは、既定のフレーバーとして `inner` が使用されている他のいくつかの分析製品とは異なります。  「[結合のフレーバー](#join-flavors)」を参照して違いを理解し、クエリによって意図した結果が得られるようにしてください。

## <a name="returns"></a>戻り値

**出力スキーマは、結合のフレーバーによって異なります。**

| 結合のフレーバー | 出力スキーマ |
|---|---|
|`kind=leftanti`, `kind=leftsemi`| 結果のテーブルには、左側の列のみが含まれます。|
| `kind=rightanti`, `kind=rightsemi` | 結果のテーブルには、右側の列のみが含まれます。|
|  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter` |  照合キーを含め、2 つのテーブルそれぞれのすべての列に対応する列。 名前が競合している場合は、右側の列の名前が自動的に変更されます。 |
   
**出力レコードは、結合のフレーバーによって異なります。**

   > [!NOTE]
   > それらのフィールドの値が同じである行が複数存在する場合は、そのすべての組み合わせの行が返されます。
   > 片方のテーブルから選択された、もう一方のテーブルに含まれる 1 つの行とすべての `on` フィールドの値が同じである行です。

| 結合のフレーバー | 出力レコード |
|---|---|
|`kind=leftanti`, `kind=leftantisemi`| 右側に一致するものがない、左側のすべてのレコードが返されます。|
| `kind=rightanti`, `kind=rightantisemi`| 左側に一致するものがない、右側のすべてのレコードが返されます。|
| `kind` 未指定、`kind=innerunique`| 左側の 1 つの行のみが `on` キーの各値に対して照合されます。 出力には、この行の一致それぞれに対応する行と、右側の行が含まれます。|
| `kind=leftsemi`| 右側に一致するものがある、左側のすべてのレコードが返されます。 |
| `kind=rightsemi`| 左側に一致するものがある、右側のすべてのレコードが返されます。 |
|`kind=inner`| 出力には、左右の一致する行のすべての組み合わせに対応する行が含まれます。 |
| `kind=leftouter` (あるいは `kind=rightouter` または `kind=fullouter`)| 一致するものがない場合も含めて、左右のすべての行に対応する行が含まれます。 一致しない出力セルには null が含まれます。 |

> [!TIP]
> パフォーマンスを最高にするには、一方のテーブルが他より常に小さい場合は、それを結合の左側 (パイプされる側) として使用します。

## <a name="example"></a>例

一部のエントリがアクティビティの開始と終了としてマークされている `login` から、延長されたアクティビティを取得します。

```kusto
let Events = MyLogTable | where type=="Event" ;
Events
| where Name == "Start"
| project Name, City, ActivityId, StartTime=timestamp
| join (Events
    | where Name == "Stop"
        | project StopTime=timestamp, ActivityId)
    on ActivityId
| project City, ActivityId, StartTime, StopTime, Duration = StopTime - StartTime
```

```kusto
let Events = MyLogTable | where type=="Event" ;
Events
| where Name == "Start"
| project Name, City, ActivityIdLeft = ActivityId, StartTime=timestamp
| join (Events
        | where Name == "Stop"
        | project StopTime=timestamp, ActivityIdRight = ActivityId)
    on $left.ActivityIdLeft == $right.ActivityIdRight
| project City, ActivityId, StartTime, StopTime, Duration = StopTime - StartTime
```

## <a name="join-flavors"></a>結合の種類

結合演算子の正確なフレーバーは、*kind* キーワードを使用して指定します。 次のフレーバーの結合演算子がサポートされています。

|結合の種類/フレーバー|[説明]|
|--|--|
|[`innerunique`](#default-join-flavor) (または既定値として空)|左側の重複を除去する内部結合|
|[`inner`](#inner-join-flavor)|標準の内部結合|
|[`leftouter`](#left-outer-join-flavor)|左外部結合|
|[`rightouter`](#right-outer-join-flavor)|右外部結合|
|[`fullouter`](#full-outer-join-flavor)|完全外部結合|
|[`leftanti`](#left-anti-join-flavor)、[`anti`](#left-anti-join-flavor)、または [`leftantisemi`](#left-anti-join-flavor)|Left Anti Join|
|[`rightanti`](#right-anti-join-flavor) または [`rightantisemi`](#right-anti-join-flavor)|Right Anti Join|
|[`leftsemi`](#left-semi-join-flavor)|左半結合|
|[`rightsemi`](#right-semi-join-flavor)|右半結合|

### <a name="default-join-flavor"></a>既定の結合フレーバー

既定の結合フレーバーは、左側の重複を除去する内部結合です。 結合の既定の実装は、2 つのイベント (それぞれが何らかのフィルター条件に一致するもの) を同じ相関 ID で関連付ける必要がある、一般的なログやトレースの分析のシナリオで役立ちます。 現象のすべての出現を取得し、関係するトレース レコードの複数の出現を無視します。

``` 
X | join Y on Key
 
X | join kind=innerunique Y on Key
```

次の 2 つのサンプル テーブルを使用して、結合の操作を説明します。

**テーブル X**

|Key |Value1
|---|---
|a |1
|b |2
|b |3
|c |4

**テーブル Y**

|Key |Value2
|---|---
|b |10
|c |20
|c |30
|d |40

既定の結合では、結合キーの左側が重複除去されてから内部結合が行われます (重複除去では最初のレコードは維持されます)。

以下のステートメントを指定します: `X | join Y on Key`

結合の有効な左側 (重複除去後のテーブル X) は次のようになります。

|Key |Value1
|---|---
|a |1
|b |2
|c |4

結合の結果は次のようになります。

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join Y on Key
```

|Key|Value1|Key1|Value2|
|---|---|---|---|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|

> [!NOTE]
> キー "a" と "d" は、左側と右側に一致するキーがなかったため、出力には含まれません。

### <a name="inner-join-flavor"></a>内部結合のフレーバー

内部結合関数は、SQL での標準の内部結合に似ています。 左側のレコードの結合キーが右側のレコードと同じである場合、出力レコードが常に生成されます。

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=inner Y on Key
```

|Key|Value1|Key1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|

> [!NOTE]
> * 右側の (b, 10) は、左側の (b, 2) および (b, 3) の両方と、2 回結合されました。
> * 左側の (c,4) は、右側の (c,20) および (c,30) の両方と、2 回結合されました。

### <a name="innerunique-join-flavor"></a>innerunique 結合フレーバー
 
左側からキーの重複を除去するには、**innerunique 結合フレーバー** を使用します。 結果の出力の行は、重複除去された左側のキーと右側のキーのすべての組み合わせになります。

> [!NOTE]
> **innerunique フレーバー** では 2 つの出力が生成される可能性があり、両方とも正しいものです。
1 つ目の出力では、join 演算子によって、t1 で出現する 1 番目のキー (値が "val1.1" のもの) がランダムに選択され、、t2 のキーと照合されます。
2 つ目の出力では、join 演算子によって、t1 で出現する 2 番目のキー (値が "val1.2" のもの) がランダムに選択され、、t2 のキーと照合されます。

```kusto
let t1 = datatable(key:long, value:string)  
[
1, "val1.1",  
1, "val1.2"  
];
let t2 = datatable(key:long, value:string)  
[  
1, "val1.3",
1, "val1.4"  
];
t1
| join kind = innerunique
    t2
on key
```

|key|value|key1|value1|
|---|---|---|---|
|1|val1.1|1|val1.3|
|1|val1.1|1|val1.4|

```kusto
let t1 = datatable(key:long, value:string)  
[
1, "val1.1",  
1, "val1.2"  
];
let t2 = datatable(key:long, value:string)  
[  
1, "val1.3", 
1, "val1.4"  
];
t1
| join kind = innerunique
    t2
on key
```

|key|value|key1|value1|
|---|---|---|---|
|1|val1.2|1|val1.3|
|1|val1.2|1|val1.4|

* Kusto は、可能であれば、`join` の後にあるフィルターを結合の適切な側 (左または右) にプッシュするように最適化されています。

* 使用されるフレーバーが **innerunique** で、フィルターが結合の左側に反映されることがあります。 フレーバーは自動的に反映され、そのフィルターに適用されるキーは常に出力に含まれます。
    
* 上記の例を使用し、`where value == "val1.2" ` フィルターを追加します。 結果は常に 2 番目のものになり、データセットの最初の結果は得られません。

```kusto
let t1 = datatable(key:long, value:string)  
[
1, "val1.1",  
1, "val1.2"  
];
let t2 = datatable(key:long, value:string)  
[  
1, "val1.3", 
1, "val1.4"  
];
t1
| join kind = innerunique
    t2
on key
| where value == "val1.2"
```

|key|value|key1|value1|
|---|---|---|---|
|1|val1.2|1|val1.3|
|1|val1.2|1|val1.4|

### <a name="left-outer-join-flavor"></a>左外部結合フレーバー

テーブル X と Y に対する左外部結合の結果には、結合条件により右側のテーブル (Y) で一致するレコードが見つからない場合でも、常に左側のテーブル (X) のすべてのレコードが含まれます。

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=leftouter Y on Key
```

|Key|Value1|Key1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|a|1|||

### <a name="right-outer-join-flavor"></a>右外部結合フレーバー

右外部結合フレーバーは左外部結合と似ていますが、テーブルの処理は逆になります。

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=rightouter Y on Key
```

|Key|Value1|Key1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||d|40|

### <a name="full-outer-join-flavor"></a>完全外部結合フレーバー

完全外部結合は、左と右の両方の外部結合を適用した場合の効果を組み合わせたものです。 結合されるテーブルのレコードが一致しない場合、結果セットには常に、一致する行がないテーブルのすべての列の `null` 値が含まれます。 一致するレコードがある場合、結果セットには、両方のテーブルから取り込まれたフィールドを含む単一の行が生成されます。

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=fullouter Y on Key
```

|Key|Value1|Key1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||d|40|
|a|1|||

### <a name="left-anti-join-flavor"></a>左反結合フレーバー

左反結合を使用すると、右側のレコードと一致しない、左側のレコードがすべて返されます。

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=leftanti Y on Key
```

|Key|Value1|
|---|---|
|a|1|

> [!NOTE]
> 反結合では "NOT IN" クエリをモデル化します。

### <a name="right-anti-join-flavor"></a>右反結合フレーバー

右反結合を使用すると、左側のレコードと一致しない、右側のレコードがすべて返されます。

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=rightanti Y on Key
```

|Key|Value2|
|---|---|
|d|40|

> [!NOTE]
> 反結合では "NOT IN" クエリをモデル化します。

### <a name="left-semi-join-flavor"></a>左半結合フレーバー

左半結合を使用すると、右側のレコードと一致する、左側のレコードがすべて返されます。 左側の列だけが返されます。

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=leftsemi Y on Key
```

|Key|Value1|
|---|---|
|b|3|
|b|2|
|c|4|

### <a name="right-semi-join-flavor"></a>右半結合フレーバー

右半結合を使用すると、左側のレコードと一致する、右側のレコードがすべて返されます。 右側の列だけが返されます。

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=rightsemi Y on Key
```

|Key|Value2|
|---|---|
|b|10|
|c|20|
|c|30|

### <a name="cross-join"></a>クロス結合

Kusto では、クロス結合フレーバーはネイティブには提供されていません。 演算子を `kind=cross` でマークすることはできません。
シミュレートするには、ダミー キーを使用します。

`X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy`

## <a name="join-hints"></a>結合のヒント

`join` 演算子では、クエリの実行方法を制御するいくつかのヒントがサポートされています。
これらのヒントによって `join` のセマンティックが変更されることはありませんが、パフォーマンスが影響を受ける可能性があります。

結合のヒントについては次のセクションで説明されています。

* `hint.shufflekey=<key>`、`hint.strategy=shuffle` - [クエリのシャッフル](shufflequery.md)
* `hint.strategy=broadcast` - [ブロードキャスト結合](broadcastjoin.md)
* `hint.remote=<strategy>` - [クラスター間の結合](joincrosscluster.md)
