---
title: ユニオン オペレータ - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのユニオン 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b62c259e1abf1ff0e0e98a90ac4da5a000db5320
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765406"
---
# <a name="union-operator"></a>union 演算子

複数のテーブルを受け取り、それらすべてのテーブルの行を返します。 

```kusto
Table1 | union Table2, Table3
```

**構文**

*[* `| union` *ユニオンパラメータ*]`kind=` `inner` | `outer`[`withsource=`[*列名*] [`isfuzzy=` `true` | `false`]*テーブル*[`,` *テーブル*]  

パイプ入力のない代替形式:

`union`[*ユニオンパラメータ*][`kind=` `inner``isfuzzy=` `true` | `false` *Table* `,` *Table**ColumnName*`withsource=`] [ 列名 ] [ ] テーブル [ テーブル ] | `outer`  

**引数**

::: zone pivot="azuredataexplorer"

* `Table`:
    *  `Events`などのテーブルの名前
    *  かっこで囲む必要があるクエリ式 。 `(Events | where id==42)` `(cluster("https://help.kusto.windows.net:443").database("Samples").table("*"))`または
    *  ワイルドカードで指定されたテーブルのセット。 たとえば、`E*`名前が始まる`E`データベース内のすべてのテーブルの和集合を形成します。
* `kind`: 
    * `inner` - 結果には、すべての入力テーブルに共通する列のサブセットが含まれます。
    * `outer`- (デフォルト)。 結果には、入力のいずれかで発生するすべての列が含まれます。 入力行で定義されていないセルは、 に`null`設定されます。
* `withsource`=*列名*: 指定した場合、出力には*ColumnName*という列が含まれ、その値は各行にどのソース テーブルが貢献したかを示します。
クエリが複数のデータベースからテーブルを効果的に参照する場合 (既定のデータベースは常にカウントされます)、この列の値はデータベースで修飾されたテーブル名になります。
同様に、複数のクラスターが参照されている場合は、クラスター__とデータベース__の条件が値に存在します。 
* `isfuzzy=``true` `isfuzzy` : に設定されている`true`場合 - ユニオンレッグのファジー解決を許可します。  |  `false` `Fuzzy`は、ソースの`union`セットに適用されます。 これは、クエリを分析して実行の準備をしているときに、共用体ソースのセットが、存在し、その時点でアクセス可能なテーブル参照のセットに縮小されることを意味します。 このようなテーブルが少なくとも 1 つ見つかった場合、解決エラーが発生すると、クエリステータスの結果に警告が表示されます (参照が欠落している場合は 1 つずつ) が、クエリの実行を妨げなくなります。解決が成功しなかった場合、クエリはエラーを返します。
既定では、 `isfuzzy=` `false`です。
* *UnionParameters*: 行一致操作と実行プランの動作を制御する*名前*`=`*値*の形式の 0 個以上 (スペースで区切られた) パラメーター。 サポートされているパラメーターは次のとおりです。 

  |名前           |値                                        |説明                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`hint.concurrency`|*数*|並列実行する演算子の同時サブクエリの数を`union`システムに示します。 *デフォルト*: クラスタの単一ノード上の CPU コアの量 (2 ~ 16)。|
  |`hint.spread`|*数値*|同時`union`実行サブクエリで使用するノード数をシステムに示します。 *デフォルト*: 1.|

::: zone-end

::: zone pivot="azuremonitor"

* `Table`:
    *  テーブルの名前(次の表)`Events`
    *  かっこで囲む必要があるクエリ式。`(Events | where id==42)`
    *  ワイルドカードで指定されたテーブルのセット。 たとえば、`E*`名前が始まる`E`データベース内のすべてのテーブルの和集合を形成します。
* `kind`: 
    * `inner` - 結果には、すべての入力テーブルに共通する列のサブセットが含まれます。
    * `outer`- (デフォルト)。 結果には、入力のいずれかで発生するすべての列が含まれます。 入力行で定義されていないセルは、 に`null`設定されます。
* `withsource`=*列名*: 指定した場合、出力には*ColumnName*という列が含まれ、その値は各行にどのソース テーブルが貢献したかを示します。
クエリが複数のデータベースからテーブルを効果的に参照する場合 (既定のデータベースは常にカウントされます)、この列の値はデータベースで修飾されたテーブル名になります。
同様に、複数のクラスターが参照されている場合、__クラスターとデータベース__の条件も値に含まれます。 
* `isfuzzy=``true` `isfuzzy` : に設定されている`true`場合 - ユニオンレッグのファジー解決を許可します。  |  `false` `Fuzzy`は、ソースの`union`セットに適用されます。 これは、クエリを分析して実行の準備をしているときに、共用体ソースのセットが、存在し、その時点でアクセス可能なテーブル参照のセットに縮小されることを意味します。 このようなテーブルが少なくとも 1 つ見つかった場合、解決エラーが発生すると、クエリステータスの結果に警告が表示されます (参照が欠落している場合は 1 つずつ) が、クエリの実行を妨げなくなります。解決が成功しなかった場合、クエリはエラーを返します。
既定では、 `isfuzzy=false`です。

::: zone-end

**戻り値**

すべての入力テーブルに存在する行と同数の行を含むテーブル。

**メモ**

::: zone pivot="azuredataexplorer"

1. `union`scope は、[ビュー キーワード](./letstatement.md)で属性が設定されている場合に[let ステートメント](./letstatement.md)を含めることができます
2. `union`スコープには[関数](../management/functions.md)が含まれません。 共用体スコープに関数を含めるには、ビューキーワードを指定して[let ステートメント](./letstatement.md)を定義[します。](./letstatement.md)
3. 入力が`union`[テーブル](../management/tables.md)([表形式の式](./tabularexpressionstatements.md)ではなく) で、`union`に[where 演算子](./whereoperator.md)が続く場合は、パフォーマンスを向上させるために、両方を[find](./findoperator.md). オペレーターによって生成されるさまざまな[出力スキーマ](./findoperator.md#output-schema)に`find`注意してください。 
4. `isfuzzy=true``union`ソースの解決フェーズにのみ適用されます。 ソース テーブルのセットが決定されると、追加のクエリ エラーが発生する可能性は抑制されません。
5. を使用`outer union`すると、結果は、入力のいずれか、名前と種類の出現ごとに 1 つの列で発生するすべての列を持ちます。 つまり、1 つの列が複数のテーブルに存在し、複数の型が含まれる場合、その列には`union`、's の結果の各型に対応する列が存在します。 この列名の末尾には、'_' の後に元の列[の型](./scalar-data-types/index.md)が付きます。

::: zone-end

::: zone pivot="azuremonitor"

1. `union`scope は、[ビュー キーワード](./letstatement.md)で属性が設定されている場合に[let ステートメント](./letstatement.md)を含めることができます
2. `union`スコープには関数は含まれません。 共用体スコープに関数を含めるには、 view[キーワード](./letstatement.md)を使用して[let ステートメント](./letstatement.md)を定義します。
3. 入力が`union`テーブル ([表形式の式](./tabularexpressionstatements.md)ではなく) で、`union`に[where 演算子](./whereoperator.md)が続く場合は、パフォーマンスを向上させるために両方を[find](./findoperator.md)に置き換えることを検討してください。 オペレーターによって生成されるさまざまな[出力スキーマ](./findoperator.md#output-schema)に`find`注意してください。 
4. `isfuzzy=``true`は、ソースの解像度のフェーズにのみ適用`union`されます。 ソース テーブルのセットが決定されると、追加のクエリ エラーが発生する可能性は抑制されません。
5. を使用`outer union`すると、結果は、入力のいずれか、名前と種類の出現ごとに 1 つの列で発生するすべての列を持ちます。 つまり、1 つの列が複数のテーブルに存在し、複数の型が含まれる場合、その列には`union`、's の結果の各型に対応する列が存在します。 この列名の末尾には、'_' の後に元の列[の型](./scalar-data-types/index.md)が付きます。

::: zone-end


**例**

```kusto
union K* | where * has "Kusto"
```

で始まる名前を持ち、任意の列`K`に が含`Kusto`まれるデータベース内のすべてのテーブルの行に.

**例**

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

**例: 使用`isfuzzy=true`**
 
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

クエリの状態を監視する - 次の警告が返されます。`Failed to resolve entity 'View_3'`

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

クエリの状態を監視する - 次の警告が返されます。`Failed to resolve entity 'SomeView*'`

**例: ソース列の型が一致しない**
 
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

サフィックス`x``_long`を`View_1`受け取った列で、結果スキーマ`x_long`に既に存在する列として、列名が重複解除され、新しい列が生成されました。`x_long1`
