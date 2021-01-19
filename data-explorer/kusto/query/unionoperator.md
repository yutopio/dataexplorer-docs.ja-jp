---
title: union 演算子 - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer の union 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 3ce7c2c09e9cd5449accfabc2a7e1cc21e4ed339
ms.sourcegitcommit: d4b359e817e002fba7320132732ce6d9cee97415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541480"
---
# <a name="union-operator"></a>union 演算子

複数のテーブルを受け取り、それらすべてのテーブルの行を返します。 

```kusto
Table1 | union Table2, Table3
```

## <a name="syntax"></a>構文

*T* `| union` [*UnionParameters*] [`kind=` `inner`|`outer`] [`withsource=`*ColumnName*] [`isfuzzy=` `true`|`false`] *Table* [`,` *Table*]...  

パイプ入力のない代替形式:

`union` [*UnionParameters*] [`kind=` `inner`|`outer`] [`withsource=`*ColumnName*] [`isfuzzy=` `true`|`false`] *Table* [`,` *Table*]...  

## <a name="arguments"></a>引数

::: zone pivot="azuredataexplorer"

* `Table`:
    *  `Events`などのテーブルの名前
    *  `(Events | where id==42)` や `(cluster("https://help.kusto.windows.net:443").database("Samples").table("*"))` など、かっこで囲む必要のあるクエリ式。または
    *  ワイルドカードで指定されたテーブルのセット。 たとえば、`E*` を使用すると、名前が `E` で始まるデータベース内のすべてのテーブルの和集合が形成されます。
* `kind`: 
    * `inner` - 結果には、すべての入力テーブルに共通する列のサブセットが含まれます。
    * `outer` - (既定値)。 結果には、入力のいずれかに存在するすべての列が含まれます。 入力行で定義されていなかったセルは `null` に設定されます。
* `withsource`=*ColumnName*:これを指定した場合は、各行の基になっているソース テーブルを示す値が格納された列 (名前は *ColumnName*) が出力に含まれます。
(ワイルドカードの照合後に) クエリから実質的に複数のデータベース (既定のデータベースでは常にカウントが行われます) のテーブルが参照される場合、この列の値はデータベースで修飾されたテーブル名になります。
同じように、複数のクラスターが参照される場合、値には __クラスターとデータベース__ の修飾が含まれます。 
* `isfuzzy=` `true` | `false`:`isfuzzy` が `true` に設定されている場合 - union 分岐のあいまい解決を許容します。 `Fuzzy` は `union` ソースのセットに適用されます。 これは、クエリの分析と実行の準備中に、union のソース セットが、その時点で存在し、アクセス可能なテーブル参照セットにまで減らされることを意味します。 そのようなテーブルが少なくとも 1 つ見つかった場合、解決に失敗すると、クエリ状態の結果に警告が生成されます (欠落している参照ごとに 1 つ)。ただし、クエリの実行が妨げられることはありません。解決に成功しなかった場合、クエリからエラーが返されます。
既定値は、`isfuzzy=` `false` です。
* *UnionParameters*:行の照合操作と実行プランの動作を制御する、"*名前* `=` *値*" という形式の 0 個以上の (空白で区切られた) パラメーター。 サポートされているパラメーターは次のとおりです。 

  |名前           |値                                        |説明                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`hint.concurrency`|*Number*|並列で実行する必要がある `union` 演算子の同時サブクエリ数をシステムに示します。 *既定*:クラスターの 1 つのノード上の CPU コア数 (2 から 16)。|
  |`hint.spread`|*Number*|`union` サブクエリの同時実行に使用されるノード数をシステムに示します。 *既定*:1.|

::: zone-end

::: zone pivot="azuremonitor"

* `Table`:
    *  `Events` などのテーブルの名前
    *  `(Events | where id==42)` など、かっこで囲む必要のあるクエリ式
    *  ワイルドカードで指定されたテーブルのセット。 たとえば、`E*` を使用すると、名前が `E` で始まるデータベース内のすべてのテーブルの和集合が形成されます。

> [!NOTE]
> テーブルのリストが分かっている場合は、ワイルドカードを使用しないでください。 ワークスペースに、非効率的な実行につながる膨大な数のテーブルが含まれている場合もあります。 時間の経過とともにテーブルが追加され、予想外の結果につながる可能性もあります。
    
* `kind`: 
    * `inner` - 結果には、すべての入力テーブルに共通する列のサブセットが含まれます。
    * `outer` - (既定値)。 結果には、入力のいずれかに存在するすべての列が含まれます。 入力行で定義されていなかったセルは `null` に設定されます。
* `withsource`=*ColumnName*:これを指定した場合は、各行の基になっているソース テーブルを示す値が格納された列 (名前は *ColumnName*) が出力に含まれます。
(ワイルドカードの照合後に) クエリから実質的に複数のデータベース (既定のデータベースでは常にカウントが行われます) のテーブルが参照される場合、この列の値はデータベースで修飾されたテーブル名になります。
同じように、複数のクラスターが参照される場合、値には __クラスターとデータベース__ の修飾が含まれます。 
* `isfuzzy=` `true` | `false`:`isfuzzy` が `true` に設定されている場合 - union 分岐のあいまい解決を許容します。 `Fuzzy` は `union` ソースのセットに適用されます。 これは、クエリの分析と実行の準備中に、union のソース セットが、その時点で存在し、アクセス可能なテーブル参照セットにまで減らされることを意味します。 そのようなテーブルが少なくとも 1 つ見つかった場合、解決に失敗すると、クエリ状態の結果に警告が生成されます (欠落している参照ごとに 1 つ)。ただし、クエリの実行が妨げられることはありません。解決に成功しなかった場合、クエリからエラーが返されます。
既定値は、`isfuzzy=false` です。

::: zone-end

## <a name="returns"></a>戻り値

すべての入力テーブルに存在する行と同数の行を含むテーブル。

**メモ**

::: zone pivot="azuredataexplorer"

1. [view キーワード](./letstatement.md)を使用して属性が指定されている場合、`union` スコープには [let ステートメント](./letstatement.md)を含めることができます。
2. `union` スコープに[関数](../management/functions.md)は含まれません。 union スコープに関数を含めるには、[view キーワード](./letstatement.md)を使用して [let ステートメント](./letstatement.md)を定義します。
3. ([表形式の式](./tabularexpressionstatements.md)とは対照的に) `union` の入力が[テーブル](../management/tables.md)であり、`union` の後に [where 演算子](./whereoperator.md)が続く場合、パフォーマンスを向上させるために、両方を [find](./findoperator.md) に置き換えることを検討してください。 `find` 演算子によって生成される異なる[出力スキーマ](./findoperator.md#output-schema)に注意してください。 
4. `isfuzzy=true` は `union` のソース解決フェーズにのみ適用されます。 ソース テーブルのセットが決定された後、追加のクエリ エラーが発生しても抑制されません。
5. `outer union` を使用すると、結果にはすべての入力で発生するすべての列 (名前と型の組み合わせごとに 1 つの列) が含まれます。 つまり、ある列が複数のテーブルに存在し、複数の型がある場合、`union` の結果には各型に対応する列があります。 この列名の末尾には、'_' と元の列の[型](./scalar-data-types/index.md)が付加されます。

::: zone-end

::: zone pivot="azuremonitor"

1. [view キーワード](./letstatement.md)を使用して属性が指定されている場合、`union` スコープには [let ステートメント](./letstatement.md)を含めることができます。
2. `union` スコープに関数は含まれません。 union スコープに関数を含めるには、[view キーワード](./letstatement.md)を使用して [let ステートメント](./letstatement.md)を定義します。
3. ([表形式の式](./tabularexpressionstatements.md)とは対照的に) `union` の入力がテーブルであり、`union` の後に [where](./whereoperator.md) 演算子が続く場合、パフォーマンスを向上させるために両方を [find](./findoperator.md) に置き換えることを検討してください。 `find` 演算子によって生成される異なる[出力スキーマ](./findoperator.md#output-schema)に注意してください。 
4. `isfuzzy=` `true` は `union` のソース解決フェーズにのみ適用されます。 ソース テーブルのセットが決定された後、追加のクエリ エラーが発生しても抑制されません。
5. `outer union` を使用すると、結果にはすべての入力で発生するすべての列 (名前と型の組み合わせごとに 1 つの列) が含まれます。 つまり、ある列が複数のテーブルに存在し、複数の型がある場合、`union` の結果には各型に対応する列があります。 この列名の末尾には、'_' と元の列の[型](./scalar-data-types/index.md)が付加されます。

::: zone-end


## <a name="example-tables-with-string-in-name-or-column"></a>例:名前または列に文字列が含まれるテーブル

```kusto
union K* | where * has "Kusto"
```

データベース内のすべてのテーブルの行のうち、名前が `K` で始まり、いずれかの列に `Kusto` という単語が含まれている行。

## <a name="example-distinct-count"></a>例:個別のカウント

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

**例:`isfuzzy=true` の使用**
 
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

クエリの状態の監視 - 次の警告が返されました: `Failed to resolve entity 'View_3'`

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

クエリの状態の監視 - 次の警告が返されました: `Failed to resolve entity 'SomeView*'`

**例: ソース列の型の不一致**
 
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

`View_1` の列 `x` はサフィックス `_long` を受け取りました。また、`x_long` という名前の列が結果スキーマに既に存在するため、列名が重複除去され、新しい列 `x_long1` が生成されました。
