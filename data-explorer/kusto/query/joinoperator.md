---
title: join 演算子-Azure データエクスプローラー |Microsoft Docs
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
ms.openlocfilehash: 2961f27226175fe81b7d9c82a6366b36134d805f
ms.sourcegitcommit: 31af2dfa75b5a2f59113611cf6faba0b45d29eb5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2020
ms.locfileid: "84454118"
---
# <a name="join-operator"></a>join 演算子

各テーブルの指定の列で値を照合することで、2 つのテーブルの行を結合し、新しいテーブルを形成します。

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

**構文**

*左テーブル* `|``join`[*Joinparameters*] `(` *右テーブル* `)` `on` *属性*

**引数**

* 左*テーブル*: 行をマージする**左側**のテーブルまたは表形式の式 (**外部**テーブルとも呼ばれます)。 として表さ `$left` れます。

* *Right table*: 行をマージする**右側**のテーブルまたは表形式の式 (**内部*テーブルとも呼ばれます)。 として表さ `$right` れます。

* *Attributes*:*左テーブル*の行が*右テーブル*の行と一致する方法を記述する1つまたは複数 (コンマ区切り) の規則。 複数のルールは、論理演算子を使用して評価され `and` ます。
  ルールには次のいずれかを指定できます。

  |ルールの種類        |構文                                          |Predicate                                                      |
  |-----------------|------------------------------------------------|---------------------------------------------------------------|
  |名前による等値 |*[ColumnName]*                                    |`where`*左テーブル*。*ColumnName* `==`*右テーブル*。*ColumnName*|
  |値による等値|`$left.`*左の列* `==``$right.`*右の列*|`where``$left.`*左の列* `==` `$right.`*右の列*       |

> [!NOTE]
> "値による等値" の場合、列名はと表記で示される適用可能な所有者テーブルで修飾*する必要があり* `$left` `$right` ます。

* *Joinparameters*: *Name* `=` 行一致操作と実行プランの動作を制御する名前*値*の形式の0個以上 (スペース区切り) のパラメーターです。 サポートされているパラメーターは次のとおりです。 

::: zone pivot="azuredataexplorer"

  |名前           |値                                        |説明                                  |
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
> が指定されていない場合、既定の結合フレーバー `kind` はに `innerunique` なります。 これは、 `inner` 既定のフレーバーとして使用されている他の分析製品とは異なります。 さまざまな種類の違いを理解し、クエリによって意図した結果が得られることを確認するには、[次](#join-flavors)の点に注意してください。

**戻り値**

出力スキーマは、結合フレーバーによって異なります。

 * `kind=leftanti`, `kind=leftsemi`:

     結果テーブルには、左側の列のみが含まれます。

     
 * `kind=rightanti`, `kind=rightsemi`:

     結果テーブルには、右側の列だけが含まれています。

     
*  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter`

     照合キーを含め、2 つのテーブルそれぞれのすべての列に対応する列。 名前が競合している場合は、右側の列の名前が自動的に変更されます。

     
出力レコードは、結合フレーバーによって異なります。

 * `kind=leftanti`, `kind=leftantisemi`

     左辺から、一致するものがないすべてのレコードを返します。     
     
 * `kind=rightanti`, `kind=rightantisemi`

     右側から、一致するものがないすべてのレコードを返します。  
      
*  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter`, `kind=leftsemi`, `kind=rightsemi`

    入力テーブル間でのすべての一致に対応する行。 一致とは、1つのテーブルから選択された行で、他のテーブルの行と同じ値を持ち、これらの制約があり `on` ます。

   - `kind`不明`kind=innerunique`

    左側の 1 つの行のみが `on` キーの各値に対して照合されます。 出力には、この行の一致それぞれに対応する行と、右側の行が含まれます。
    
   - `kind=leftsemi`
   
    右側から一致するすべてのレコードを左側から返します。
    
   - `kind=rightsemi`
   
       左辺からの一致を持つすべてのレコードを返します。

   - `kind=inner`
 
    出力には、左右の一致行のすべての組み合わせに対応する行が含まれます。

   - `kind=leftouter` (あるいは `kind=rightouter` または `kind=fullouter`)

    内部の一致のほかに、左側と右側のどちらか一方または両方のすべての行に対応する行が含まれます (一致するものがない場合も)。 その場合、一致しない出力セルには null が含まれます。
    それらのフィールドの値が同じである行が複数存在する場合は、そのすべての組み合わせの行が返されます。

 

**ヒント**

パフォーマンスを最大限高めるためのヒントを示します。

* 一方のテーブルがもう一方よりも常に小さい場合は、それを結合の左側 (パイプされる側) として使います。

**例**

アクティビティの開始と終了がマークされているエントリが含まれるログから、延長されたアクティビティを取得します。 

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

[詳細について](./samples.md#get-sessions-from-start-and-stop-events)は、こちらを参照してください。

## <a name="join-flavors"></a>結合の種類

Join 演算子の正確なフレーバーは、kind キーワードを使用して指定します。 現時点では、Kusto は次の種類の結合演算子をサポートしています。 

|結合の種類|説明|
|--|--|
|[`innerunique`](#default-join-flavor)(または既定値として空)|左側の重複除去による内部結合|
|[`inner`](#inner-join)|標準内部結合|
|[`leftouter`](#left-outer-join)|左外部結合|
|[`rightouter`](#right-outer-join)|右外部結合|
|[`fullouter`](#full-outer-join)|完全外部結合|
|[`leftanti`](#left-anti-join)、、 [`anti`](#left-anti-join) または[`leftantisemi`](#left-anti-join)|左のアンチジョイン|
|[`rightanti`](#right-anti-join)もしくは[`rightantisemi`](#right-anti-join)|右のアンチジョイン|
|[`leftsemi`](#left-semi-join)|左半結合|
|[`rightsemi`](#right-semi-join)|右半結合|

### <a name="inner-and-innerunique-join-operator-flavors"></a>内部および innerunique 結合演算子のフレーバー

-    **内部結合フレーバー**を使用する場合は、左と右の一致する行の組み合わせごとに、左側のキー deduplications のない行が出力に表示されます。 出力は、左辺と右辺のキーのデカルト積になります。
    **内部結合**の例:

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
| join kind = inner
    t2
on key
```

|キー|value|key1|value1|
|---|---|---|---|
|1|val 1.2|1|val 1.3|
|1|val 1.1|1|val 1.3|
|1|val 1.2|1|val 1.4|
|1|val 1.1|1|val 1.4|

-    **Innerunique 結合フレーバー**を使用すると、左側からキーが重複除去され、重複除去の左キーと右キーのすべての組み合わせからの出力に行が表示されます。
    前に使用したのと同じデータセットに対する**innerunique join**の例では、このケースの**innerunique フレーバー**によって2つの出力が生成され、両方とも正しいことがあることに注意してください。
    最初の出力では、結合演算子は、値が "val 1.1" で t1 に出現し、t2 キーと一致する最初のキーをランダムに選択します。2番目のキーでは、2番目のキーをランダムに選択し、値が "val 1.2" で、t2 キーと照合されます。

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

|キー|value|key1|value1|
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

|キー|value|key1|value1|
|---|---|---|---|
|1|val 1.2|1|val 1.3|
|1|val 1.2|1|val 1.4|


-    Kusto は、 `join` 可能な限り、適切な結合側の左側または右側に向かうフィルターをプッシュするように最適化されています。
    使用されるフレーバーが**innerunique**で、フィルターを結合の左側に反映できる場合、そのフレーバーは自動的に反映され、そのフィルターに適用されるキーは常に出力に表示されます。
    たとえば、上記の例を使用し、フィルターを追加すると、 ` where value == "val1.2" ` 常に2番目の結果が得られ、使用されるデータセットの最初の結果は得られません。

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

|キー|value|key1|value1|
|---|---|---|---|
|1|val 1.2|1|val 1.3|
|1|val 1.2|1|val 1.4|
 
### <a name="default-join-flavor"></a>既定の結合フレーバー
    
    X | join Y on Key
    X | join kind=innerunique Y on Key
     
2つのサンプルテーブルを使用して、結合の操作を説明します。 
 
テーブル X 

|Key |Value1 
|---|---
|a |1 
|b |2 
|b |3 
|c |4 

テーブル Y 

|Key |Value2 
|---|---
|b |10 
|c |20 
|c |30 
|d |40 
 
既定の結合では、結合キーの左側が重複除去されてから内部結合が行われます (重複除去では最初のレコードは維持されます)。 以下のステートメントを指定します。 

    X | join Y on Key 

この場合、結合の有効な左側は (重複除去後のテーブル X) は次のようになります。 

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


(左右両方に一致するキーがないため、"a" と "d" キーは出力に表示されないことに注意してください)。 
 
(これまでは、Kusto の最初のバージョンでサポートされていた結合の最初の実装でした。これは、2つのイベント (それぞれがフィルター条件に一致します) を同じ相関 ID で関連付け、対象となるトレースレコードの複数の情報を無視して、探しているすべての現象を元に戻します。
 
### <a name="inner-join"></a>内部結合

これは、SQL の世界では周知の標準的な内部結合です。 左側のレコードの結合キーが右側のレコードと同じである場合は、常に出力レコードが生成されます。 
 
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

右側にある (b, 10) が2回結合されていることに注意してください。 (b, 2) と (b, 3) が左側にあります。左側の (c、4) も2回結合されていました。 (c, 20) と (c, 30) の両方が右側にあります。 

### <a name="left-outer-join"></a>左外部結合 

テーブル X と Y の左外部結合の結果には、結合条件で右テーブル (Y) に一致するレコードが見つからない場合でも、常に左テーブル (X) のすべてのレコードが含まれます。 
 
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

 
### <a name="right-outer-join"></a>右外部結合 

左外部結合と似ていますが、テーブルの処理は逆になります。 
 
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

 
### <a name="full-outer-join"></a>完全外部結合 

概念的には、完全外部結合は、左と右の両方の外部結合を適用した場合の効果を組み合わせたものです。 結合テーブルのレコードが一致しない場合、結果セットには、一致する行がないテーブルのすべての列に対して NULL 値が含まれます。 一致するレコードがある場合は、結果セットに単一行が生成されます (両方のテーブルからデータが取り込まれたフィールドを含む)。 
 
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

 
### <a name="left-anti-join"></a>左のアンチジョイン

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

|Key|Value1|
|---|---|
|a|1|

反結合では "NOT IN" クエリをモデル化します。 

### <a name="right-anti-join"></a>右のアンチジョイン

Right アンチ結合では、左側のレコードと一致しない右側のすべてのレコードが返されます。 
 
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

反結合では "NOT IN" クエリをモデル化します。 

### <a name="left-semi-join"></a>左半結合

左半結合では、左側から、右側のレコードに一致するすべてのレコードが返されます。 左側の列だけが返されます。 

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

### <a name="right-semi-join"></a>右半結合

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

|Key|Value2|
|---|---|
|b|10|
|c|20|
|c|30|


### <a name="cross-join"></a>クロス結合

Kusto は、クロス結合フレーバーをネイティブに提供しません (つまり、演算子をでマークすることはできません `kind=cross` )。
ただし、ダミーキーを使用すると、これをシミュレートするのは困難です。

    X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy

## <a name="join-hints"></a>結合ヒント

演算子は、 `join` クエリの実行方法を制御するいくつかのヒントをサポートしています。
これらはのセマンティックを変更しません `join` が、パフォーマンスに影響を与える可能性があります。

結合ヒントは、次の記事で説明されています。 
* `hint.shufflekey=<key>`と `hint.strategy=shuffle`  -  [シャッフルクエリ](shufflequery.md)
* `hint.strategy=broadcast` - [ブロードキャスト結合](broadcastjoin.md)
* `hint.remote=<strategy>` - [クラスター間結合](joincrosscluster.md)
