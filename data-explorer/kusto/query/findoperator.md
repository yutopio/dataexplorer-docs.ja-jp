---
title: オペレーターを検索する - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの検索演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4c3db23128c47c86639f15286cbcbcb748157386
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765912"
---
# <a name="find-operator"></a>find 演算子

一連のテーブルで述語と一致する行を検索します。

::: zone pivot="azuredataexplorer"

のスコープは`find`、クロスデータベースまたはクロスクラスターにすることもできます。

```kusto
find in (Table1, Table2, Table3) where Fruit=="apple"

find in (database('*').*) where Fruit == "apple"

find in (cluster('cluster_name').database('MyDB*'.*)) where Fruit == "apple"
```

::: zone-end

::: zone pivot="azuremonitor"

```kusto
find in (Table1, Table2, Table3) where Fruit=="apple"
```

::: zone-end

## <a name="syntax"></a>構文

* `find`[`withsource`=*列名*][`in``(`*Table*テーブル`,`,*テーブル*, .]`)`] `where`*述語*`project-smart`  |  `project` [`:`*列名*`,` [*列の種類*] [*列名*]`:`列*の型*][`,` `pack(*)`]] 

* `find`*述語*`project-smart` | `project`[`:`*列名*[`,` *列の種類*] [*列名*[`:`*列タイプ*][`, pack(*)`]] 

## <a name="arguments"></a>引数

::: zone pivot="azuredataexplorer"

* `withsource=`*列名*: オプション。 既定では、出力には、各行にどのソース テーブルが貢献したかを示す値を持つ*source_* と呼ばれる列が含まれます。 指定した場合は、 の代わりに*ColumnName* *source_* 使用されます。
クエリが複数のデータベースからテーブルを効果的に参照する場合 (既定のデータベースは常にカウントされます)、この列の値はデータベースで修飾されたテーブル名になります。 同様に、複数のクラスターが参照されている場合は、クラスター__とデータベース__の条件が値に存在します。
* *述語*:[詳細を参照してください](./findoperator.md#predicate-syntax)。 `boolean`入力テーブルの列に対する[式](./scalar-data-types/bool.md) *[テーブル*] [`,` *テーブル*, .]]これは、各入力テーブルの各行に対して評価されます。 
* `Table`: 省略可能。 既定では、現在のデータベース内のすべてのテーブルで*検索*されます。
    *  テーブルの名前 (または など`Events`)
    *  クエリ式 ( `(Events | where id==42)`
    *  ワイルドカードで指定されたテーブルのセット。 たとえば、`E*`名前が始まる`E`データベース内のすべてのテーブルの和集合を形成します。
* `project-smart` | `project`:[see details](./findoperator.md#output-schema)指定`project-smart`されていない場合は詳細を参照すると、既定で使用されます

::: zone-end

::: zone pivot="azuremonitor"

* `withsource=`*列名*: オプション。 既定では、出力には、各行にどのソース テーブルが貢献したかを示す値を持つ*source_* と呼ばれる列が含まれます。 指定した場合は、 の代わりに*ColumnName* *source_* 使用されます。
* *述語*:[詳細を参照してください](./findoperator.md#predicate-syntax)。 `boolean`入力テーブルの列に対する[式](./scalar-data-types/bool.md) *[テーブル*] [`,` *テーブル*, .]]これは、各入力テーブルの各行に対して評価されます。 
* `Table`: 省略可能。 デフォルトでは *、検索*はすべてのテーブルで検索されます
    *  テーブルの名前 (または など`Events`)
    *  クエリ式 ( `(Events | where id==42)`
    *  ワイルドカードで指定されたテーブルのセット。 たとえば、`E*`名前が始まる`E`すべてのテーブルの和集合を形成します。
* `project-smart` | `project`:[see details](./findoperator.md#output-schema)指定`project-smart`されていない場合は詳細を参照すると、既定で使用されます

::: zone-end

## <a name="returns"></a>戻り値

*述語*が表 [`,` *Table**テーブル*、 .. `true`] の行の変換 。 行は、以下に示すように出力スキーマに従って変換されます。

## <a name="output-schema"></a>出力スキーマ

**source_列**

find 演算子の出力には、常にソース テーブル名を持つ*source_* 列が含まれます。 このパラメーターを使用して列の`withsource`名前を変更できます。

**結果列**

述語の評価で使用される列が含まれていないソース テーブルは、フィルターで除外されます。

を使用`project-smart`する場合、出力に表示される列は次のようになります。
1. 述語に明示的に表示される列
2. すべてのフィルター処理されたテーブルに共通の列 残りの列はプロパティ バッグにパックされ、追加`pack_`の列に表示されます。
述語によって明示的に参照され、複数の型を持つ複数のテーブルに表示される列は、このような型ごとに結果スキーマに異なる列を持ちます。 各列名は、元の列名と型から、アンダースコアで区切って作成されます。

*列名*[`:`*列の種類*`,` ] [`:`*列名*[*列の種類*] を使用`project`する場合[`,` `pack(*)`]:
1. 結果表には、リストで指定された列が含まれます。 ソース テーブルに特定の列が含まれていない場合、対応する行の値は null になります。
2. *ColumnName*と一緒に*ColumnType*を指定すると **、結果**のこの列は指定された型を持ち、必要に応じて値はその型にキャストされます。 *述語*を評価する場合、列の種類に影響はありません。
3. 使用`pack(*)`すると、残りの列はプロパティ バッグにパックされ、追加`pack_`の列に表示されます。

**pack_列**

この列には、出力スキーマに表示されないすべての列のデータを含むプロパティ バッグが含まれます。 ソース列名はプロパティ名として機能し、列の値はプロパティ値として機能します。

## <a name="predicate-syntax"></a>述語構文

いくつかのフィルタリング関数の概要については[、where演算子](./whereoperator.md)を参照してください。

find 演算子は`* has`*用語*の代替構文をサポートし、*用語*のみを使用すると、すべての入力列にわたって用語を検索します。

## <a name="notes"></a>メモ

* 句が`project`複数のテーブルに含まれる列を参照し、複数の型を持つ場合、型は project 句のこの列参照に従う必要があります。
* 列が複数のテーブルに表示され、複数の型`project-smart`を持ち、使用されている場合は`find`[、's](./unionoperator.md)の結果に対応する列が 's の結果に含まれます。
* *project-smart*を使用する場合、述語、ソース テーブル セット、またはテーブル スキーマ内での変更によって、出力スキーマが変更される場合があります。 定数結果スキーマが必要な場合は、代わりに*プロジェクト*を使用します。
* `find`scope に[関数](../management/functions.md)を含む必要はありません。 検索スコープに関数を含めるには、 [view キーワード](./letstatement.md)を使用して[let ステートメント](./letstatement.md)を定義します。

## <a name="performance-tips"></a>パフォーマンスに関するヒント

* [表形式の式](./tabularexpressionstatements.md)ではなく[テーブル](../management/tables.md)を使用する - 表形式の式の場合、find`union`演算子はクエリにフォールバックし、パフォーマンスが低下する可能性があります
* 複数のテーブルに表示され、複数の型を持つ列がプロジェクト節の一部である場合、渡す前にテーブルを変更する場合よりも、プロジェクト句に`find` *ColumnType*を追加することをおすすめします (前のヒントを参照)。
* 述部に時間ベースのフィルターを追加する (日時列の値または[ingestion_time()](./ingestiontimefunction.md)を使用)
* 全文検索よりも特定の列で検索することを好む 
* 複数のテーブルに表示され、複数の型を持つ列を明示的に参照しないことをお好みで指定します。 述語が、複数の型の列型を解決するときに有効な場合、クエリは UNION にフォールバックします。

[例を参照してください。](./findoperator.md#examples-of-cases-where-find-will-perform-as-union)

## <a name="examples"></a>例

::: zone pivot="azuredataexplorer"

###  <a name="term-lookup-across-all-tables-in-current-database"></a>すべてのテーブルでの用語検索 (現在のデータベース内)

次のクエリは、現在のデータベース内のすべてのテーブルから、任意の列にが含まれる`Kusto`すべての行を検索します。 結果のレコードは[、 出力スキーマ](#output-schema)に従って変換されます。 

```kusto
find "Kusto"
```

## <a name="term-lookup-across-all-tables-matching-a-name-pattern-in-the-current-database"></a>名前パターンに一致するすべてのテーブルでの用語検索 (現在のデータベース内)

次のクエリは、現在のデータベース内のすべてのテーブルから、名前が`K`で始まり、任意の列にが含`Kusto`まれるすべての行を検索します。
結果のレコードは[、 出力スキーマ](#output-schema)に従って変換されます。 

```kusto
find in (K*) where * has "Kusto"
```

###  <a name="term-lookup-across-all-tables-in-all-databases-in-the-cluster"></a>すべてのデータベース (クラスター内) のすべてのテーブルでの用語検索

次のクエリは、すべての列にが含`Kusto`まれるすべてのデータベースのすべてのテーブルからすべての行を検索します。 これはクロス[データベース クエリ](./cross-cluster-or-database-queries.md)です。 結果のレコードは[、 出力スキーマ](#output-schema)に従って変換されます。 

```kusto
find in (database('*').*) "Kusto"
```

### <a name="term-lookup-across-all-tables-and-databases-matching-a-name-pattern-in-the-cluster"></a>名前パターンに一致するすべてのテーブルおよびデータベース間での用語検索 (クラスター内)

次のクエリは、名前がで始まり`K``B`、任意の列にが含`Kusto`まれるすべてのデータベースで名前が始まるすべてのテーブルからすべての行を検索します。 結果のレコードは[、 出力スキーマ](#output-schema)に従って変換されます。 

```kusto
find in (database("B*").K*) where * has "Kusto"
```

### <a name="term-lookup-in-several-clusters"></a>複数のクラスターでの用語検索

次のクエリは、名前がで始まり`K``B`、任意の列にが含`Kusto`まれるすべてのデータベースで名前が始まるすべてのテーブルからすべての行を検索します。 結果のレコードは[、 出力スキーマ](#output-schema)に従って変換されます。 

```kusto
find in (cluster("cluster1").database("B*").K*, cluster("cluster2").database("C*".*))
where * has "Kusto"
```

::: zone-end

::: zone pivot="azuremonitor"

### <a name="term-lookup-across-all-tables"></a>すべてのテーブルでの用語検索

次のクエリは、すべての列にが含`Kusto`まれるすべてのテーブルからすべての行を検索します。 結果のレコードは[、 出力スキーマ](#output-schema)に従って変換されます。 

```kusto
find "Kusto"
```

::: zone-end

## <a name="examples-of-find-output-results"></a>出力結果`find`の例  

次の例は、2 つのテーブルで使用できる方法`find`を示しています: イベント テーブル 1 とイベント テーブル 2。 次の 2 つのテーブルの内容があるとします。 

### <a name="eventstable1"></a>イベントテーブル1

|Session_Id|Level|イベントテキスト|Version
|---|---|---|---|
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|情報|一部のテキスト1|v1.0.0
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|一部のテキスト2|v1.0.0
|28b8e46e-3c31-43cf-83cb-48921c3986fc|Error|一部のテキスト3|v1.0.1
|8f057b11-3281-45c3-a856-05ebb18a3c59|情報|一部のテキスト4|v1.1.0

### <a name="eventstable2"></a>イベントテーブル2

|Session_Id|Level|イベントテキスト|EventName
|---|---|---|---|
|f7d5f95f-f580-4ea6-830b-5776c8d64fd|情報|その他のテキスト1|イベント1
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|情報|その他のテキスト2|イベント2
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|その他のテキスト3|イベント3
|15eeaab5-8576-4b58-8fc6-478f75d8fee4|Error|その他のテキスト4|イベント4


### <a name="search-in-common-columns-project-common-and-uncommon-columns-and-pack-the-rest"></a>共通の列、プロジェクト共通列と一般的でない列で検索し、残りをパック  

```kusto
find in (EventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' and Level == 'Error' 
     project EventText, Version, EventName, pack(*)
```

|source_|イベントテキスト|Version|EventName|pack_
|---|---|---|---|---|
|イベントテーブル1|一部のテキスト2|v1.0.0||{"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e","レベル":"エラー"}
|イベントテーブル2|その他のテキスト3||イベント3|{"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e","レベル":"エラー"}


### <a name="search-in-common-and-uncommon-columns"></a>共通列と一般的でない列で検索する

```kusto
find Version == 'v1.0.0' or EventName == 'Event1' project Session_Id, EventText, Version, EventName
```

|source_|Session_Id|イベントテキスト|Version|EventName|
|---|---|---|---|---|
|イベントテーブル1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|一部のテキスト1|v1.0.0
|イベントテーブル1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|一部のテキスト2|v1.0.0
|イベントテーブル2|f7d5f95f-f580-4ea6-830b-5776c8d64fd|その他のテキスト1||イベント1

注意: 実際には *、EventsTable1*行は述語```Version == 'v1.0.0'```でフィルター処理され *、EventsTable2*行は```EventName == 'Event1'```述語でフィルター処理されます。

### <a name="use-abbreviated-notation-to-search-across-all-tables-in-the-current-database"></a>現在のデータベース内のすべてのテーブルを検索するには、省略形の表記を使用します。

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

|source_|Session_Id|Level|イベントテキスト|pack_|
|---|---|---|---|---|
|イベントテーブル1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|情報|一部のテキスト1|{"バージョン":"v1.0.0"}
|イベントテーブル1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|一部のテキスト2|{"バージョン":"v1.0.0"}
|イベントテーブル2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|情報|その他のテキスト2|{"イベント名":"イベント2"}
|イベントテーブル2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Error|その他のテキスト3|{"イベント名":"イベント3"}


### <a name="return-the-results-from-each-row-as-a-property-bag"></a>各行の結果をプロパティ バッグとして返す

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' project pack(*)
```

|source_|pack_|
|---|---|
|イベントテーブル1|{"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e","レベル":"情報","イベントテキスト":"いくつかのテキスト1","バージョン":"v1.0.0"}
|イベントテーブル1|{"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e","レベル":"エラー","イベントテキスト":"いくつかのテキスト2","バージョン":"v1.0.0"}
|イベントテーブル2|{"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e"、「レベル":"情報」、「イベントテキスト":"その他のテキスト2」、「イベント名":"イベント名」"イベント名":"イベント2"
|イベントテーブル2|{"Session_Id":"acbd207d-51aa-4df7-bfa7-be70eb68f04e"、"レベル":"エラー"、"イベントテキスト":"その他のテキスト3"、"イベント名":"イベント名":"イベント3"}


## <a name="examples-of-cases-where-find-will-perform-as-union"></a>として実行`find`する場合の例`union`

### <a name="using-a-non-tabular-expression-as-find-operand"></a>表形式以外の式を検索オペランドとして使用する

```kusto
let PartialEventsTable1 = view() { EventsTable1 | where Level == 'Error' };
find in (PartialEventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

### <a name="referencing-a-column-that-appears-in-multiple-tables-and-has-multiple-types"></a>複数のテーブルに表示され、複数の型を持つ列を参照する

次の 2 つのテーブルを実行して作成したとします。 

```kusto
.create tables 
  Table1 (Level:string, Timestamp:datetime, ProcessId:string),
  Table2 (Level:string, Timestamp:datetime, ProcessId:int64)
```

* 次のクエリは、 として`union`実行されます。
```kusto
find in (Table1, Table2) where ProcessId == 1001
```

出力結果スキーマは __(レベル:文字列、タイムスタンプ、ProcessId_string、ProcessId_int) になります。__

* 次のクエリも、別の結果スキーマとして`union`実行されますが、結果スキーマが生成されます。
```kusto
find in (Table1, Table2) where ProcessId == 1001 project Level, Timestamp, ProcessId:string 
```

出力結果スキーマは __(レベル:文字列、タイムスタンプ、ProcessId_string) になります。__
