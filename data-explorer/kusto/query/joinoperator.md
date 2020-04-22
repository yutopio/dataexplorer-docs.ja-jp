---
title: 結合オペレーター - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの結合演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 86d883c8d0214d278099260fa2406b91b776b4d1
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765819"
---
# <a name="join-operator"></a>join 演算子

各テーブルの指定の列で値を照合することで、2 つのテーブルの行を結合し、新しいテーブルを形成します。

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

**構文**

*左テーブル*`|``join`[*結合パラメータ*] `(` *右テーブル*`)``on`*属性*

**引数**

* *LeftTable*: 行がマージされる**左側**のテーブルまたは表形式の式 (**外部**テーブルとも呼ばれます)。 として表記`$left`されます。

* *右テーブル*: 行をマージする**右側**のテーブルまたは表形式の式 *(内部テーブル*とも呼ばれます)。 として表記`$right`されます。

* *属性*: *LeftTable*の行が*RightTable*の行とどのように一致するかを記述する 1 つ以上の (コンマ区切り) ルール。 論理演算子を使用して複数`and`の規則が評価されます。
  ルールは次のいずれかになります。

  |ルールの種類        |構文                                          |Predicate                                                      |
  |-----------------|------------------------------------------------|---------------------------------------------------------------|
  |名前による等しい |*[ColumnName]*                                    |`where`*左テーブル*.*列名*`==`*の右テーブル*。*列名*|
  |値による等しい値|`$left.`*左列*`==``$right.`*右列*|`where``$left.`*左*`==`列`$right.`*右列*       |

> [!NOTE]
> '値による等しい' の場合、列名は、表記と`$right`表記で示される適切な所有者テーブル`$left`で修飾*する必要があります*。

* *JoinParameters*: 行一致操作と実行プランの動作を制御する*名前*`=`*値*の形式の 0 個以上 (スペースで区切られた) パラメーター。 サポートされているパラメーターは次のとおりです。 

::: zone pivot="azuredataexplorer"

  |名前           |値                                        |説明                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`kind`         |結合の種類|[「結合フレーバー」を参照してください。](#join-flavors)|                                             |
  |`hint.remote`  |`auto`, `left`, `local`, `right`              |[クラスター間結合を](joincrosscluster.md)参照してください。|
  |`hint.strategy`|実行のヒント                               |[「結合ヒント](#join-hints)」を参照してください。                |

::: zone-end

::: zone pivot="azuremonitor"

  |名前           |値                                        |説明                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`kind`         |結合の種類|[「結合フレーバー」を参照してください。](#join-flavors)|                                             |
  |`hint.remote`  |`auto`, `left`, `local`, `right`              |                                             |
  |`hint.strategy`|実行のヒント                               |[「結合ヒント](#join-hints)」を参照してください。                |

::: zone-end


> [!WARNING]
> 既定の結合フレーバー (`kind`指定されていない場合)`innerunique`は です。 これは、デフォルトのフレーバーとして持つ他の分析`inner`製品とは異なります。 異なる種類の違いを理解し、クエリが意図した結果を生成することを確認するために[、以下](#join-flavors)をよく読んでください。

**戻り値**

出力スキーマは、結合のフレーバーによって異なります。

 * `kind=leftanti`, `kind=leftsemi`:

     結果表には、左側からの列のみが含まれます。

     
 * `kind=rightanti`, `kind=rightsemi`:

     結果表には、右側からの列のみが含まれます。

     
*  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter`

     照合キーを含め、2 つのテーブルそれぞれのすべての列に対応する列。 名前が競合している場合は、右側の列の名前が自動的に変更されます。

     
出力レコードは、結合フレーバーによって異なります。

 * `kind=leftanti`, `kind=leftantisemi`

     右側から一致しないレコードをすべて左側から返します。     
     
 * `kind=rightanti`, `kind=rightantisemi`

     左側から一致しないレコードをすべて右側から返します。  
      
*  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter`, `kind=leftsemi`, `kind=rightsemi`

    入力テーブル間でのすべての一致に対応する行。 一致は、次の制約を持つ他の`on`テーブルの行と同じ値を持つ 1 つのテーブルから選択された行です。

   - `kind`未指定`kind=innerunique`

    左側の 1 つの行のみが `on` キーの各値に対して照合されます。 出力には、この行の一致それぞれに対応する行と、右側の行が含まれます。
    
   - `kind=leftsemi`
   
    右側から一致するレコードをすべて左側から返します。
    
   - `kind=rightsemi`
   
       左から一致するレコードを右側からすべて返します。

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

[この例の詳細](./samples.md#activities)については、をクリックしてください。

## <a name="join-flavors"></a>結合の種類

結合演算子の正確なフレーバーは、kind キーワードで指定します。 今日の時点で、Kusto は結合演算子の次のフレーバーをサポートしています。 

|結合の種類|説明|
|--|--|
|[`innerunique`](#default-join-flavor)(またはデフォルトとして空)|左側の重複除去を使用した内部結合|
|[`inner`](#inner-join)|標準内部結合|
|[`leftouter`](#left-outer-join)|左外部結合|
|[`rightouter`](#right-outer-join)|右外部結合|
|[`fullouter`](#full-outer-join)|完全外部結合|
|[`leftanti`](#left-anti-join)、[`anti`](#left-anti-join)または[`leftantisemi`](#left-anti-join)|左反結合|
|[`rightanti`](#right-anti-join)または[`rightantisemi`](#right-anti-join)|右反結合|
|[`leftsemi`](#left-semi-join)|左半結合|
|[`rightsemi`](#right-semi-join)|右セミ結合|

### <a name="inner-and-innerunique-join-operator-flavors"></a>内部および内部の一意の結合演算子のフレーバー

-    **内部結合フレーバー**を使用する場合、左キーの重複除去を行わずに、左と右から一致する行のすべての組み合わせに対して、出力に行が表示されます。 出力は、左右のキーのデカルト積になります。
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

|key|value|key1|value1|
|---|---|---|---|
|1|val1.2|1|val1.3|
|1|val1.1|1|val1.3|
|1|val1.2|1|val1.4|
|1|val1.1|1|val1.4|

-    **内部固有の結合フレーバー**を使用すると、左側からキーが重複除去され、重複除去された左キーと右キーのすべての組み合わせから出力に行が表示されます。
    上記で使用したのと同じデータセットの**内部固有結合**の例として、この場合の**内部固有のフレーバー**は2つの出力が可能であり、両方とも正しいことに注意してください。
    最初の出力では、結合演算子は t1 に "val1.1" という値を持つ最初のキーをランダムに選択し、2 番目のキーと一致させますが、2 番目のキーでは 2 番目のキーをランダムに選択すると、"val1.2" という値を持つ t1 にランダムに表示され、t2 キーと一致します。

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


-    Kustoは、可能な場合は、適切な結合`join`側の左または右に向かって後に来るフィルタをプッシュするように最適化されています。
    使用されるフレーバーが**innerunique**で、フィルターが結合の左側に伝播できる場合、そのフィルターが自動的に伝播され、そのフィルターに適用されるキーが常に出力に表示されます。
    たとえば、上記の例を使用してフィルタ` where value == "val1.2" `を追加すると、常に 2 番目の結果が得られ、使用するデータセットの最初の結果は決して得られなくなるでしょう。

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
 
### <a name="default-join-flavor"></a>既定の結合フレーバー
    
    X | join Y on Key
    X | join kind=innerunique Y on Key
     
2 つのサンプル テーブルを使用して、結合の操作について説明します。 
 
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


(左側と右側の両方に一致するキーがなかったので、キー 'a' と 'd' は出力に表示されません)。 
 
(歴史的に、これは Kusto の初期バージョンでサポートされる結合の最初の実装であり、同じ相関 ID で 2 つのイベント (それぞれが一部のフィルタリング基準に一致する) を関連付け、原因となるトレース レコードの複数の出現を無視して、探している現象のすべての出現を取り戻す一般的なログ/トレース分析シナリオで役立ちます。
 
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

右側から来る (b,10) は、(b,2) と (b,3) の両方を左側に 2 回結合していることに注意してください。また、左側の (c,4) は、右側に (c,20) と (c,30) の両方を使用して 2 回結合されました。 

### <a name="left-outer-join"></a>左外部結合 

テーブル X および Y の左外部結合の結果には、結合条件が右テーブル (Y) に一致するレコードが見つからない場合でも、常に左テーブル (X) のすべてのレコードが含まれます。 
 
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

概念的には、完全外部結合は、左と右の両方の外部結合を適用した場合の効果を組み合わせたものです。 結合されたテーブルのレコードが一致しない場合、結果セットは、一致する行がないテーブルのすべての列に対して NULL 値を持ちます。 一致するレコードがある場合は、結果セットに単一行が生成されます (両方のテーブルからデータが取り込まれたフィールドを含む)。 
 
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

 
### <a name="left-anti-join"></a>左反結合

左反結合は、右側のレコードと一致しないすべてのレコードを左側から返します。 
 
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

### <a name="right-anti-join"></a>右反結合

右の反結合は、右側のレコードと一致しないすべてのレコードを右側から返します。 
 
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

左セミ結合は、右側のレコードと一致するすべてのレコードを左側から返します。 左側の列のみが返されます。 

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

### <a name="right-semi-join"></a>右セミ結合

右セミ結合は、右側のレコードと一致するすべてのレコードを左側から返します。 右側の列のみが返されます。 

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

Kusto は、ネイティブでクロス結合のフレーバーを提供しません`kind=cross`(つまり、演算子にマークを付けることができません)。
ただし、ダミーキーを考え出してこれをシミュレートすることは難しいことではありません。

    X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy

## <a name="join-hints"></a>参加のヒント

この`join`演算子は、クエリの実行方法を制御するヒントを多数サポートしています。
これらの値は`join`のセマンティクスを変更しませんが、パフォーマンスに影響を与える可能性があります。

次の記事で説明されている結合ヒント: 
* `hint.shufflekey=<key>`と`hint.strategy=shuffle` - [シャッフル クエリ](shufflequery.md)
* `hint.strategy=broadcast` - [ブロードキャスト参加](broadcastjoin.md)
* `hint.remote=<strategy>` - [クロスクラスター結合](joincrosscluster.md)
