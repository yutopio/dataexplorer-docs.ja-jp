---
title: join 演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの join 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4952e315a974e72135c722b255a96f57bf89cc12
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86058781"
---
# <a name="join-operator"></a>join 演算子

2つのテーブルの行をマージして、各テーブルの指定した列の値を照合することにより、新しいテーブルを作成します。

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

## <a name="syntax"></a>構文

*左テーブル* `|``join`[*Joinparameters*] `(` *右テーブル* `)` `on` *属性*

## <a name="arguments"></a>引数

* 左*テーブル*: 行をマージする**左側**のテーブルまたは表形式の式 (**外部**テーブルとも呼ばれます)。 として表さ `$left` れます。

* *Right table*: 行をマージする、**右側**のテーブルまたは表形式の式 (**内部**テーブルとも呼ばれます)。 として表さ `$right` れます。

* *属性*:*左テーブル*から行を*右テーブル*の行と照合する方法を記述する1つまたは複数のコンマで区切られた**ルール**です。 複数のルールは、論理演算子を使用して評価され `and` ます。

  **ルール**には次のいずれかを指定できます。

  |ルールの種類        |構文          |Predicate    |
  |-----------------|--------------|-------------------------|
  |名前による等値 |*[ColumnName]*    |`where`*左テーブル*。*ColumnName* `==`*右テーブル*。*ColumnName*|
  |値による等値|`$left.`*左の列* `==``$right.`*右の列*|`where``$left.`*左の列* `==` `$right.`*右の列*       |

    > [!NOTE]
    > "値による等値" の場合、列名はと表記で示される適用可能な所有者テーブルで修飾されて*いる必要があり* `$left` `$right` ます。

* *Joinparameters*: *Name* `=` 行一致操作と実行プランの動作を制御する名前*値*の形式で、空白で区切られた0個以上のパラメーター。 サポートされているパラメーターは次のとおりです。

    ::: zone pivot="azuredataexplorer"

    |パラメーター名           |値                                        |説明                                  |
    |---------------|----------------------------------------------|---------------------------------------------|
    |`kind`         |結合の種類|「[結合フレーバー](#join-flavors) 」を参照してください。|                                             |
    |`hint.remote`  |`auto`, `left`, `local`, `right`              |[クラスター間結合を](joincrosscluster.md)参照|
    |`hint.strategy`|実行ヒント                               |[結合ヒント](#join-hints)を確認する                |

    ::: zone-end

    ::: zone pivot="azuremonitor"

    |名前           |値                                        |説明                                  |
    |---------------|----------------------------------------------|---------------------------------------------|
    |`kind`         |結合の種類|「[結合フレーバー](#join-flavors) 」を参照してください。|                                             |
    |`hint.remote`  |`auto`, `left`, `local`, `right`              |                                             |
    |`hint.strategy`|実行ヒント                               |[結合ヒント](#join-hints)を確認する                |

    ::: zone-end

> [!WARNING]
> が `kind` 指定されていない場合、既定の結合フレーバーはに `innerunique` なります。 これは `inner` 、既定のフレーバーとして機能する他の分析製品とは異なります。  違いを理解し、クエリが意図した結果を得られるようにするには、「[結合フレーバー](#join-flavors) 」を参照してください。

## <a name="returns"></a>戻り値

**出力スキーマは、結合フレーバーによって異なります。**

| 結合フレーバー | 出力スキーマ |
|---|---|
|`kind=leftanti`, `kind=leftsemi`| 結果テーブルには、左側の列のみが含まれます。|
| `kind=rightanti`, `kind=rightsemi` | 結果テーブルには、右側の列だけが含まれています。|
|  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter` |  照合キーを含め、2 つのテーブルそれぞれのすべての列に対応する列。 名前が競合している場合は、右側の列の名前が自動的に変更されます。 |
   
**出力レコードは、結合フレーバーによって異なります。**

   > [!NOTE]
   > それらのフィールドの値が同じである行が複数存在する場合は、そのすべての組み合わせの行が返されます。
   > 片方のテーブルから選択された、もう一方のテーブルに含まれる 1 つの行とすべての `on` フィールドの値が同じである行です。

| 結合フレーバー | 出力レコード |
|---|---|
|`kind=leftanti`, `kind=leftantisemi`| 左側から、一致するものがないすべてのレコードを返します。|
| `kind=rightanti`, `kind=rightantisemi`| 右側から、一致するものがないすべてのレコードを返します。|
| `kind`不明`kind=innerunique`| 左側の 1 つの行のみが `on` キーの各値に対して照合されます。 出力には、この行の一致それぞれに対応する行と、右側の行が含まれます。|
| `kind=leftsemi`| 右側から一致するすべてのレコードを左側から返します。 |
| `kind=rightsemi`| 左辺からの一致を持つすべてのレコードを返します。 |
|`kind=inner`| 左右の一致する行の組み合わせごとに、出力に行を格納します。 |
| `kind=leftouter` (あるいは `kind=rightouter` または `kind=fullouter`)| 一致するものがない場合でも、左右のすべての行の行が含まれます。 一致しない出力セルに null が含まれています。 |

> [!TIP]
> 最適なパフォーマンスを得るために、1つのテーブルが常に他より小さい場合は、結合の左 (パイプ) 側として使用します。

## <a name="example"></a>例

`login`アクティビティの開始と終了としてマークされているエントリを含む拡張アクティビティをから取得します。

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

Join 演算子の正確なフレーバーは、 *kind*キーワードを使用して指定します。 次の種類の結合演算子がサポートされています。

|結合の種類/フレーバー|説明|
|--|--|
|[`innerunique`](#default-join-flavor)(または既定値として空)|左側の重複除去による内部結合|
|[`inner`](#inner-join-flavor)|標準内部結合|
|[`leftouter`](#left-outer-join-flavor)|左外部結合|
|[`rightouter`](#right-outer-join-flavor)|右外部結合|
|[`fullouter`](#full-outer-join-flavor)|完全外部結合|
|[`leftanti`](#left-anti-join-flavor)、 [`anti`](#left-anti-join-flavor) 、または[`leftantisemi`](#left-anti-join-flavor)|左のアンチジョイン|
|[`rightanti`](#right-anti-join-flavor) または [`rightantisemi`](#right-anti-join-flavor)|右のアンチジョイン|
|[`leftsemi`](#left-semi-join-flavor)|左半結合|
|[`rightsemi`](#right-semi-join-flavor)|右半結合|

### <a name="default-join-flavor"></a>既定の結合フレーバー

既定の結合フレーバーは、左側の重複除去による内部結合です。 既定の結合実装は、2つのイベント (それぞれがフィルター条件に一致するもの) を同じ相関 ID で関連付けることができる、一般的なログ/トレース分析のシナリオで役立ちます。 すべての現象を元に戻し、関与するトレースレコードの複数の外観を無視します。

``` 
X | join Y on Key
 
X | join kind=innerunique Y on Key
```

次の2つのサンプルテーブルは、結合の操作を説明するために使用されます。

**テーブル X**

|キー |Value1
|---|---
|a |1
|b |2
|b |3
|c |4

**テーブル Y**

|キー |Value2
|---|---
|b |10
|c |20
|c |30
|d |40

既定の結合では、結合キーの左側を解除した後に内部結合が行われます (重複除去では、最初のレコードが保持されます)。

次のステートメントを指定します。`X | join Y on Key`

結合の有効な左側 (重複除去後のテーブル X) は次のようになります。

|キー |Value1
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

|キー|Value1|Key1|Value2|
|---|---|---|---|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|

> [!NOTE]
> 左右両方に一致するキーがないため、キー ' a ' と ' d ' は出力に表示されません。

### <a name="inner-join-flavor"></a>内部結合フレーバー

内部結合関数は、SQL ワールドからの標準の内部結合に似ています。 左側のレコードが右側のレコードと同じ結合キーを持っている場合は常に、出力レコードが生成されます。

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

|キー|Value1|Key1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|

> [!NOTE]
> * 右側から (b, 10) が2回結合されました。左側には (b, 2) と (b, 3) の両方があります。
> * 左側に (c, 4) が2回結合されました。 (c, 20) と (c, 30) の両方が右側にあります。

### <a name="innerunique-join-flavor"></a>Innerunique-結合フレーバー
 
左端からキーを重複除去するには **、innerunique 結合フレーバー**を使用します。 結果は、重複除去左キーと右キーのすべての組み合わせからの出力の行になります。

> [!NOTE]
> **innerunique フレーバー**は2つの出力を生成でき、両方とも正しいことがあります。
最初の出力では、結合演算子によって t1 に表示される最初のキーがランダムに選択され、値が "val 1.1" で、t2 キーと一致しています。
2番目の出力では、結合演算子は t1 に表示される2番目のキーをランダムに選択します。値は "val 1.2" で、t2 キーと一致します。

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
|1|val 1.1|1|val 1.3|
|1|val 1.1|1|val 1.4|

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
|1|val 1.2|1|val 1.3|
|1|val 1.2|1|val 1.4|

* Kusto は、 `join` 可能であれば、適切な結合側 (左または右) に、の後に来るフィルターをプッシュするように最適化されています。

* 場合によっては、使用されるフレーバーが**innerunique**で、フィルターが結合の左側に反映されることがあります。 フレーバーは自動的に反映され、そのフィルターに適用されるキーは常に出力に表示されます。
    
* 上記の例を使用し、フィルターを追加し `where value == "val1.2" ` ます。 常に2番目の結果が得られ、データセットの最初の結果は得られません。

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
|1|val 1.2|1|val 1.3|
|1|val 1.2|1|val 1.4|

### <a name="left-outer-join-flavor"></a>左外部結合フレーバー

テーブル X と Y の左外部結合の結果には、結合条件で右側のテーブル (Y) に一致するレコードが見つからない場合でも、常に左テーブル (X) のすべてのレコードが含まれます。

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

|キー|Value1|Key1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|a|1|||

### <a name="right-outer-join-flavor"></a>右外部結合フレーバー

右外部結合フレーバーは左外部結合に似ていますが、テーブルの処理は逆になっています。

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

|キー|Value1|Key1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||d|40|

### <a name="full-outer-join-flavor"></a>完全外部結合フレーバー

完全外部結合は、左結合と右外部結合の両方を適用した場合の効果を組み合わせたものです。 結合されたテーブル内のレコードが一致しない場合、結果セットには、一致する行がない `null` テーブルのすべての列の値が含まれます。 一致するレコードについては、両方のテーブルからデータを格納するフィールドを含む1行が結果セットに生成されます。

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

|キー|Value1|Key1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||d|40|
|a|1|||

### <a name="left-anti-join-flavor"></a>左のアンチ結合フレーバー

左のアンチ結合では、左側から、右側のレコードと一致しないすべてのレコードが返されます。

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

|キー|Value1|
|---|---|
|a|1|

> [!NOTE]
> 反結合では "NOT IN" クエリをモデル化します。

### <a name="right-anti-join-flavor"></a>右のアンチジョインフレーバー

右のアンチ結合では、左側のレコードと一致しない右側のすべてのレコードが返されます。

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

|キー|Value2|
|---|---|
|d|40|

> [!NOTE]
> 反結合では "NOT IN" クエリをモデル化します。

### <a name="left-semi-join-flavor"></a>左半結合フレーバー

左半結合は、左側から、右側のレコードと一致するすべてのレコードを返します。 左側の列だけが返されます。

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

|キー|Value1|
|---|---|
|b|3|
|b|2|
|c|4|

### <a name="right-semi-join-flavor"></a>右半結合フレーバー

右半結合では、左側のレコードに一致する右側のすべてのレコードが返されます。 右側の列だけが返されます。

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

|キー|Value2|
|---|---|
|b|10|
|c|20|
|c|30|

### <a name="cross-join"></a>クロス結合

Kusto は、クロス結合フレーバーをネイティブに提供しません。 演算子をでマークすることはできません `kind=cross` 。
をシミュレートするには、ダミーキーを使用します。

`X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy`

## <a name="join-hints"></a>結合ヒント

演算子は、 `join` クエリの実行方法を制御するいくつかのヒントをサポートしています。
これらのヒントはのセマンティックを変更しません `join` が、パフォーマンスに影響を与える可能性があります。

結合ヒントについては、次の記事で説明します。

* `hint.shufflekey=<key>`と `hint.strategy=shuffle`  -  [シャッフルクエリ](shufflequery.md)
* `hint.strategy=broadcast` - [ブロードキャスト結合](broadcastjoin.md)
* `hint.remote=<strategy>` - [クラスター間結合](joincrosscluster.md)
