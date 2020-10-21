---
title: union 演算子-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの union 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b3b7d571662d8a9ed0fd592547f32a131d26e277
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245747"
---
# <a name="union-operator"></a>union 演算子

複数のテーブルを受け取り、それらすべてのテーブルの行を返します。 

```kusto
Table1 | union Table2, Table3
```

## <a name="syntax"></a>構文

*T* `| union` [*UnionParameters*] [ `kind=` `inner` | `outer` ] [ `withsource=` *ColumnName*] [ `isfuzzy=` `true` | `false` ]*テーブル*[ `,` *テーブル*]...  

パイプ入力のない代替形式:

`union`[*UnionParameters*][ `kind=` `inner` | `outer` ] [ `withsource=` *ColumnName*] [ `isfuzzy=` `true` | `false` ]*テーブル*[ `,` *テーブル*]...  

## <a name="arguments"></a>引数

::: zone pivot="azuredataexplorer"

* `Table`:
    *  `Events`などのテーブルの名前
    *  またはなど、かっこで囲む必要があるクエリ式 `(Events | where id==42)` `(cluster("https://help.kusto.windows.net:443").database("Samples").table("*"))` 。
    *  ワイルドカードで指定されたテーブルのセット。 たとえば、 `E*` は、名前が始まるデータベース内のすべてのテーブルの和集合を形成し `E` ます。
* `kind`: 
    * `inner` - 結果には、すべての入力テーブルに共通する列のサブセットが含まれます。
    * `outer` -(既定値)。 結果には、すべての入力に出現するすべての列が含まれます。 入力行で定義されていないセルはに設定され `null` ます。
* `withsource`=*Columnname*: 指定した場合、出力には、行を提供したソーステーブルを示す *ColumnName* という列が含まれます。
(ワイルドカードの照合後に) クエリが複数のデータベースのテーブルを参照している場合 (既定のデータベースは常にカウントされます)、この列の値は、データベースで修飾されたテーブル名になります。
同じように、複数のクラスターが参照されている場合は、 __クラスターとデータベース__ の両方の要件が値に存在します。 
* `isfuzzy=``true`  |  `false` : がに設定されている場合は `isfuzzy` `true` 、共用体の区間のあいまい解決を許可します。 `Fuzzy` ソースのセットに適用さ `union` れます。 これは、クエリを分析し、実行の準備をしている間に、一連の共用体のソースが存在し、その時点でアクセス可能なテーブル参照のセットに縮小されることを意味します。 少なくとも1つのテーブルが見つかった場合、解決エラーが発生すると、クエリの状態の結果 (参照が不足している場合は1つ) に警告が生成されますが、クエリの実行が妨げられることはありません。解決策が成功しなかった場合、クエリはエラーを返します。
既定値は、`isfuzzy=` `false` です。
* *UnionParameters*: *Name* `=` 行一致操作と実行プランの動作を制御する名前*値*の形式の0個以上 (スペース区切り) のパラメーターです。 サポートされているパラメーターは次のとおりです。 

  |名前           |値                                        |説明                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`hint.concurrency`|*数値*|システムに対して、演算子の同時実行サブクエリの数を並列で実行するかどうかを `union` 指定します。 *既定値*: クラスターの単一ノードの CPU コアの量 (2 ~ 16)。|
  |`hint.spread`|*数値*|同時実行のサブクエリによって使用されるノードの数をシステムにヒントし `union` ます。 *既定値*は1です。|

::: zone-end

::: zone pivot="azuremonitor"

* `Table`:
    *  テーブルの名前 (など) `Events`
    *  かっこで囲む必要があるクエリ式 (例) `(Events | where id==42)`
    *  ワイルドカードで指定されたテーブルのセット。 たとえば、 `E*` は、名前が始まるデータベース内のすべてのテーブルの和集合を形成し `E` ます。
* `kind`: 
    * `inner` - 結果には、すべての入力テーブルに共通する列のサブセットが含まれます。
    * `outer` -(既定値)。 結果には、すべての入力に出現するすべての列が含まれます。 入力行で定義されていないセルはに設定され `null` ます。
* `withsource`=*Columnname*: 指定した場合、出力には、各行を提供したソーステーブルを示す値を持つ *ColumnName* という列が含まれます。
(ワイルドカードの照合後に) クエリが複数のデータベースのテーブルを参照している場合 (既定のデータベースは常にカウントされます)、この列の値は、データベースで修飾されたテーブル名になります。
同様に、複数のクラスターが参照されている場合は、 __クラスターとデータベース__ の要件が値に含まれます。 
* `isfuzzy=``true`  |  `false` : がに設定されている場合は `isfuzzy` `true` 、共用体の区間のあいまい解決を許可します。 `Fuzzy` ソースのセットに適用さ `union` れます。 これは、クエリを分析し、実行の準備をしている間に、一連の共用体のソースが存在し、その時点でアクセス可能なテーブル参照のセットに縮小されることを意味します。 少なくとも1つのテーブルが見つかった場合、解決エラーが発生すると、クエリの状態の結果 (参照が不足している場合は1つ) に警告が生成されますが、クエリの実行が妨げられることはありません。解決策が成功しなかった場合、クエリはエラーを返します。
既定値は、`isfuzzy=false` です。

::: zone-end

## <a name="returns"></a>戻り値

すべての入力テーブルに存在する行と同数の行を含むテーブル。

**ノート**

::: zone pivot="azuredataexplorer"

1. `union`[view キーワード](./letstatement.md)で属性が指定されている場合、スコープには[let ステートメント](./letstatement.md)を含めることができます
2. `union` スコープには、 [関数](../management/functions.md)は含まれません。 Union スコープに関数を含めるには、 [let ステートメント](./letstatement.md)を[view キーワード](./letstatement.md)と共に定義します。
3. `union`入力が[テーブル](../management/tables.md)([表形式式](./tabularexpressionstatements.md)に oppose) であり、の後に where 演算子が続く場合、パフォーマンスを向上させるには、 `union` 両方を[find](./findoperator.md)に置き換えることを検討[where operator](./whereoperator.md)してください。 演算子によって生成された別の [出力スキーマ](./findoperator.md#output-schema) に注意して `find` ください。 
4. `isfuzzy=true` ソースの解決フェーズにのみ適用さ `union` れます。 ソーステーブルのセットが決定されると、追加のクエリエラーが抑制されません。
5. を使用する場合、結果には、 `outer union` すべての入力で発生するすべての列が含まれます。名前と型のオカレンスごとに1つの列があります。 つまり、1つの列が複数のテーブルに存在し、複数の型がある場合、その列の結果の型ごとに対応する列が作成され `union` ます。 この列名の末尾には、' _ '、元の列の [型](./scalar-data-types/index.md)が続きます。

::: zone-end

::: zone pivot="azuremonitor"

1. `union`[view キーワード](./letstatement.md)で属性が指定されている場合、スコープには[let ステートメント](./letstatement.md)を含めることができます
2. `union` スコープには、関数は含まれません。 Union スコープに関数を含めるには- [view キーワード](./letstatement.md)を使用して[let ステートメント](./letstatement.md)を定義する
3. `union`入力がテーブル ([表形式式](./tabularexpressionstatements.md)に oppose) であり、の後に where 演算子が指定されている場合は、 `union` 両方を[find](./findoperator.md)に置き換えることをお勧めします。 [where operator](./whereoperator.md) 演算子によって生成された別の [出力スキーマ](./findoperator.md#output-schema) に注意してください `find` 。 
4. `isfuzzy=``true`ソース解決のフェーズにのみ適用さ `union` れます。 ソーステーブルのセットが決定されると、追加のクエリエラーが抑制されません。
5. を使用する場合、結果には、 `outer union` すべての入力で発生するすべての列が含まれます。名前と型のオカレンスごとに1つの列があります。 つまり、1つの列が複数のテーブルに存在し、複数の型がある場合、その列の結果の型ごとに対応する列が作成され `union` ます。 この列名の末尾には、' _ '、元の列の [型](./scalar-data-types/index.md)が続きます。

::: zone-end


## <a name="example-tables-with-string-in-name-or-column"></a>例: 名前または列に文字列が含まれるテーブル

```kusto
union K* | where * has "Kusto"
```

名前がで始まるデータベース内のすべてのテーブルの行。この場合、 `K` 列にはという単語が含まれ `Kusto` ます。

## <a name="example-distinct-count"></a>例: Distinct count

```kusto
union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```

過去 1 日で `Query` イベントまたは `Command` イベントを発生させた個別のユーザーの数。 結果の "SourceTable" 列は "Query" または "Command" を指します。

```kusto
Query
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

より効率的なこのバージョンでも同じ結果が生成されます。 和集合を作成する前に各テーブルをフィルター処理します。

**例: を使用する `isfuzzy=true`**
 
```kusto     
// Using union isfuzzy=true to access non-existing view:                                     
let View_1 = view () { print x=1 };
let View_2 = view () { print x=1 };
let OtherView_1 = view () { print x=1 };
union isfuzzy=true
(View_1 | where x > 0), 
(View_2 | where x > 0),
(View_3 | where x > 0)
| count 
```

|Count|
|---|
|2|

クエリの状態の監視-次の警告が返されました: `Failed to resolve entity 'View_3'`

```kusto
// Using union isfuzzy=true and wildcard access:
let View_1 = view () { print x=1 };
let View_2 = view () { print x=1 };
let OtherView_1 = view () { print x=1 };
union isfuzzy=true View*, SomeView*, OtherView*
| count 
```

|Count|
|---|
|3|

クエリの状態の監視-次の警告が返されました: `Failed to resolve entity 'SomeView*'`

**例: ソース列の型が一致しません**
 
```kusto     
let View_1 = view () { print x=1 };
let View_2 = view () { print x=toint(2) };
union withsource=TableName View_1, View_2
```

|TableName|x_long|x_int|
|---------|------|-----|
|View_1   |1     |     |
|View_2   |      |2    |

```kusto     
let View_1 = view () { print x=1 };
let View_2 = view () { print x=toint(2) };
let View_3 = view () { print x_long=3 };
union withsource=TableName View_1, View_2, View_3 
```

|TableName|x_long1|x_int |x_long|
|---------|-------|------|------|
|View_1   |1      |      |      |
|View_2   |       |2     |      |
|View_3   |       |      |3     |

列はサフィックスを受け取り、と `x` `View_1` `_long` いう名前の列が `x_long` 結果スキーマに既に存在するため、列名が重複除去され、新しい列が生成されます。 `x_long1`
