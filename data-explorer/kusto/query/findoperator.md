---
title: 検索演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの検索演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4be61920fe22c7b77eb54f849e86ba06a8bf533b
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763813"
---
# <a name="find-operator"></a>find 演算子

テーブルのセット全体の述語に一致する行を検索します。

::: zone pivot="azuredataexplorer"

のスコープは、 `find` データベース間またはクロスクラスターにすることもできます。

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

* `find`[ `withsource` = *ColumnName*] [ `in` `(` *テーブル*[ `,` *テーブル*,...] `)` ] `where`*述語*[ `project-smart`  |  `project` *columnname* [ `:` *ColumnType*] [ `,` *columnname*[ `:` *ColumnType*],...][`,` `pack(*)`]] 

* `find`*述語*[ `project-smart`  |  `project` *columnname*[ `:` *ColumnType*] [ `,` *columnname*[ `:` *ColumnType*],...][`, pack(*)`]] 

## <a name="arguments"></a>引数

::: zone pivot="azuredataexplorer"

* `withsource=`*ColumnName*: 省略可能。 既定では、出力には、各行を提供したソーステーブルを示す値を持つ*source_* という名前の列が含まれます。 指定した場合、 *source_* の代わりに*ColumnName*が使用されます。
ワイルドカードの照合後、クエリで複数のデータベース (既定のデータベースを含む) のテーブルを参照している場合、この列の値には、データベースで修飾されたテーブル名が付けられます。 同じように、複数のクラスターが参照されている場合は、*クラスター*と*データベース*の両方の要件が値に存在します。
* *述語*: `boolean` [expression](./scalar-data-types/bool.md)入力テーブル*テーブル*[ `,` *table*,...] の列に対する式。各入力テーブルの各行に対して評価されます。 詳細については、「[述語-構文の詳細](./findoperator.md#predicate-syntax)」を参照してください。
* `Table`:省略可能。 既定では、[*検索*] では、現在のデータベース内のすべてのテーブルが検索されます。
    *  テーブルの名前 (など)`Events`
    *  クエリ式 ( `(Events | where id==42)`
    *  ワイルドカードで指定されたテーブルのセット。 たとえば、 `E*` は、名前がで始まるデータベース内のすべてのテーブルの和集合を形成し `E` ます。
* `project-smart` | `project`: 指定されていない場合 `project-smart` は、既定でが使用されます。 詳細については、「[出力スキーマの詳細](./findoperator.md#output-schema)」を参照してください。

::: zone-end

::: zone pivot="azuremonitor"

* `withsource=`*ColumnName*: 省略可能。 既定では、出力には、各行を提供したソーステーブルを示す値を持つ*source_* という名前の列が含まれます。 指定した場合、 *source_* の代わりに*ColumnName*が使用されます。
* *述語*: `boolean` [expression](./scalar-data-types/bool.md)入力テーブル*テーブル*[ `,` *table*,...] の列に対する式。各入力テーブルの各行に対して評価されます。 詳細については、「[述語-構文の詳細](./findoperator.md#predicate-syntax)」を参照してください。
* `Table`:省略可能。 既定では、*検索*によってすべてのテーブルが検索されます。
    *  テーブルの名前 (など)`Events` 
    *  クエリ式 ( `(Events | where id==42)`
    *  ワイルドカードで指定されたテーブルのセット。 たとえば、は、 `E*` 名前がで始まるすべてのテーブルの和集合を形成し `E` ます。
* `project-smart` | `project`: 指定されていない場合 `project-smart` 、既定で使用されます。 詳細については、「[出力スキーマの詳細](./findoperator.md#output-schema)」を参照してください。

::: zone-end

## <a name="returns"></a>戻り値

述語がである*テーブル*[ `,` *table*,...] の行*Predicate*の変換 `true` 。 行は、[出力スキーマ](#output-schema)に従って変換されます。

## <a name="output-schema"></a>出力スキーマ

**source_ 列**

検索演算子の出力には、常にソーステーブル名を含む*source_* 列が含まれます。 列の名前を変更するには、パラメーターを使用し `withsource` ます。

**結果列**

述語の評価で使用される列が含まれていないソーステーブルはフィルターで除外されます。

を使用すると `project-smart` 、出力に表示される列は次のようになります。
* 述語に明示的に表示される列。
* すべてのフィルター選択されたテーブルに共通の列。

残りの列はプロパティバッグにパックされ、追加の列に表示され `pack_` ます。
述語によって明示的に参照され、複数の型を持つ複数のテーブルに存在する列は、そのような型ごとに、結果スキーマに異なる列を持ちます。 各列の名前は、元の列の名前と型からアンダースコアで区切られて構築されます。

`project` *Columnname*[ `:` *ColumnType*] [ `,` *columnname*[ `:` *ColumnType*],...] を使用する場合[`,` `pack(*)`]:
* 結果テーブルには、一覧に指定された列が含まれます。 ソーステーブルに特定の列が含まれていない場合、対応する行の値は null になります。
* *ColumnName*で*ColumnType*を指定すると、"result" 内のこの列には指定された型が割り当てられ、必要に応じて値がその型にキャストされます。 キャストは、*述語*を評価するときに列の型に影響を与えません。
* を使用すると、 `pack(*)` 残りの列はプロパティバッグにパックされ、追加の列に表示され `pack_` ます。

**pack_ 列**

この列には、出力スキーマに表示されないすべての列のデータを含むプロパティバッグが格納されます。 ソース列の名前はプロパティ名として使用され、列の値はプロパティ値として機能します。

## <a name="predicate-syntax"></a>述語の構文

*検索*演算子は、用語の代替構文をサポートしてい `* has` ます。また、*語句*を使用すると、すべての入力列で用語が検索されます。

フィルター処理関数の概要については、「 [where 演算子](./whereoperator.md)」を参照してください。

## <a name="notes"></a>メモ

* 句が `project` 複数のテーブルに出現し、複数の型を持つ列を参照している場合、型は project 句のこの列参照に従う必要があります。
* 複数のテーブルに1つの列があり、複数の型があり、使用されている場合 `project-smart` 、「 `find` [union](./unionoperator.md) 」で説明されているように、の結果にはそれぞれの型に対応する列があります。
* *プロジェクト-スマート*を使用する場合、述語内の変更、ソーステーブルセット、またはテーブルスキーマ内の変更により、出力スキーマが変更されることがあります。 定数の結果スキーマが必要な場合は、代わりに*project*を使用してください。
* `find`スコープに[関数](../management/functions.md)を含めることはできません。 検索範囲に関数を含めるには、 [let ステートメント](./letstatement.md)を[view キーワード](./letstatement.md)と共に定義します。

## <a name="performance-tips"></a>パフォーマンスに関するヒント

* 表[形式の式](./tabularexpressionstatements.md)とは異なり、[テーブル](../management/tables.md)を使用します。
表形式の式の場合、検索演算子は、 `union` パフォーマンス低下の原因となる可能性のあるクエリにフォールバックします。
* 複数のテーブルに表示され、複数の型を持つ列では、が project 句の一部である場合、テーブルをに渡す前に、 *ColumnType*を project 句に追加して、テーブルを変更することをお勧め `find` します。
* 述語に時間ベースのフィルターを追加します。 Datetime 列の値または[ingestion_time ()](./ingestiontimefunction.md)を使用します。
* フルテキスト検索ではなく、特定の列を検索します。
* 複数のテーブルに出現し、複数の型を持つ列を参照しないことをお勧めします。 複数の型に対してこのような列の型を解決するときに述語が有効な場合、クエリは union にフォールバックします。
たとえば、[検索が共用体として機能するケースの例](./findoperator.md#examples-of-cases-where-find-will-act-as-union)を参照してください。
 
## <a name="examples"></a>使用例

::: zone pivot="azuredataexplorer"

### <a name="term-lookup-across-all-tables-in-current-database"></a>現在のデータベース内のすべてのテーブルにおける用語参照

クエリでは、現在のデータベース内のすべてのテーブルからすべての行が検索されます。この列には、という単語が含まれてい `Kusto` ます。
結果のレコードは、[出力スキーマ](#output-schema)に従って変換されます。

```kusto
find "Kusto"
```

## <a name="term-lookup-across-all-tables-matching-a-name-pattern-in-the-current-database"></a>現在のデータベース内の名前パターンに一致するすべてのテーブル間での用語参照

このクエリでは、現在のデータベース内の、名前がで始まるすべてのテーブルからすべての行が検索され `K` ます。このとき、列にはという単語が含まれてい `Kusto` ます。
結果のレコードは、[出力スキーマ](#output-schema)に従って変換されます。

```kusto
find in (K*) where * has "Kusto"
```

### <a name="term-lookup-across-all-tables-in-all-databases-in-the-cluster"></a>クラスター内のすべてのデータベース内のすべてのテーブルにわたる用語参照

このクエリでは、すべてのデータベースのすべてのテーブルからすべての行が検索されます。すべての列には、という単語が含まれてい `Kusto` ます。
このクエリは、[複数のデータベースにまたがる](./cross-cluster-or-database-queries.md)クエリです。
結果のレコードは、[出力スキーマ](#output-schema)に従って変換されます。

```kusto
find in (database('*').*) "Kusto"
```

### <a name="term-lookup-across-all-tables-and-databases-matching-a-name-pattern-in-the-cluster"></a>クラスター内の名前のパターンに一致するすべてのテーブルおよびデータベースにわたる用語参照

このクエリでは、名前がで始まるすべてのデータベースで名前がで始まるすべてのテーブルからすべての行が検索されます。その際、列にはと `K` `B` いう単語が含まれてい `Kusto` ます。
結果のレコードは、[出力スキーマ](#output-schema)に従って変換されます。

```kusto
find in (database("B*").K*) where * has "Kusto"
```

### <a name="term-lookup-in-several-clusters"></a>複数のクラスターでの用語参照

このクエリでは、名前がで始まるすべてのデータベースで名前がで始まるすべてのテーブルからすべての行が検索されます。その際、列にはと `K` `B` いう単語が含まれてい `Kusto` ます。
結果のレコードは、[出力スキーマ](#output-schema)に従って変換されます。

```kusto
find in (cluster("cluster1").database("B*").K*, cluster("cluster2").database("C*".*))
where * has "Kusto"
```

::: zone-end

::: zone pivot="azuremonitor"

### <a name="term-lookup-across-all-tables"></a>すべてのテーブルにわたる用語参照

このクエリでは、すべてのテーブルから、という単語が含まれているすべての行が検索され `Kusto` ます。
結果のレコードは、[出力スキーマ](#output-schema)に従って変換されます。

```kusto
find "Kusto"
```

::: zone-end

## <a name="examples-of-find-output-results"></a>`find`出力結果の例  

次の例は、 `find` *EventsTable1*と*EventsTable2*の2つのテーブルでを使用する方法を示しています。
これら2つのテーブルの次のコンテンツがあるとします。

### <a name="eventstable1"></a>EventsTable1

|Session_Id|Level|EventText|Version
|---|---|---|---|
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Information|一部の Text1|v 1.0.0
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|エラー|一部の Text2|v 1.0.0
|28b8e46e-3c31-43cf-83cb-48921c3986fc|エラー|テキストテキスト|v1.0
|8f057b11-3281-45c3-a856-05ebb18a3c59|Information|いくつかのテキスト|v 1.1.0

### <a name="eventstable2"></a>EventsTable2

|Session_Id|Level|EventText|EventName
|---|---|---|---|
|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|Information|その他のテキストテキスト|Event1
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Information|その他の Text2|Event2
|acbd207d-51aa-4df7-bfa7-be70eb68f04e|エラー|その他のテキストテキスト|Event3
|15eaeab5-8576-4b58-8fc6-478f75d8fee4|エラー|その他のいくつかのテキスト|Event4

### <a name="search-in-common-columns-project-common-and-uncommon-columns-and-pack-the-rest"></a>共通の列を検索し、一般的な列と一般的でない列をプロジェクトに保存し、残りをパックします。  

```kusto
find in (EventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' and Level == 'Error' 
     project EventText, Version, EventName, pack(*)
```

|source_|EventText|Version|EventName|pack_
|---|---|---|---|---|
|EventsTable1|一部の Text2|v 1.0.0||{"Session_Id": "acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level": "Error"}
|EventsTable2|その他のテキストテキスト||Event3|{"Session_Id": "acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level": "Error"}


### <a name="search-in-common-and-uncommon-columns"></a>一般的な列と一般的でない列の検索

```kusto
find Version == 'v1.0.0' or EventName == 'Event1' project Session_Id, EventText, Version, EventName
```

|source_|Session_Id|EventText|Version|EventName|
|---|---|---|---|---|
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|一部の Text1|v 1.0.0
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|一部の Text2|v 1.0.0
|EventsTable2|f7d5f95f-f580-4ea6-830b-5776c8d64fdd|その他のテキストテキスト||Event1

注: 実際には、 *EventsTable1*行は述語を使用してフィルター処理され、 ```Version == 'v1.0.0'``` *EventsTable2*行は述語を使用してフィルター処理され ```EventName == 'Event1'``` ます。

### <a name="use-abbreviated-notation-to-search-across-all-tables-in-the-current-database"></a>省略形の表記を使用して、現在のデータベース内のすべてのテーブルを検索します。

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

|source_|Session_Id|Level|EventText|pack_|
|---|---|---|---|---|
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Information|一部の Text1|{"Version": "v 1.0.0"}
|EventsTable1|acbd207d-51aa-4df7-bfa7-be70eb68f04e|エラー|一部の Text2|{"Version": "v 1.0.0"}
|EventsTable2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|Information|その他の Text2|{"EventName": "Event2"}
|EventsTable2|acbd207d-51aa-4df7-bfa7-be70eb68f04e|エラー|その他のテキストテキスト|{"EventName": "Event3"}


### <a name="return-the-results-from-each-row-as-a-property-bag"></a>各行の結果をプロパティバッグとして返します。

```kusto
find Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e' project pack(*)
```

|source_|pack_|
|---|---|
|EventsTable1|{"Session_Id": "acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level": "Information", "EventText": "Some Text1", "Version": "v 1.0.0"}
|EventsTable1|{"Session_Id": "acbd207d-51aa-4df7-bfa7-be70eb68f04e"、"Level": "Error"、"EventText": "Some Text2"、"Version": "v 1.0.0"}
|EventsTable2|{"Session_Id": "acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level": "Information", "EventText": "その他の Text2", "EventName": "Event2"}
|EventsTable2|{"Session_Id": "acbd207d-51aa-4df7-bfa7-be70eb68f04e", "Level": "Error", "EventText": "その他のテキスト文字", "EventName": "Event3"}


## <a name="examples-of-cases-where-find-will-act-as-union"></a>`find`がとして機能するケースの例`union`

### <a name="using-a-non-tabular-expression-as-find-operand"></a>テーブル式以外の式を検索オペランドとして使用する

```kusto
let PartialEventsTable1 = view() { EventsTable1 | where Level == 'Error' };
find in (PartialEventsTable1, EventsTable2) 
     where Session_Id == 'acbd207d-51aa-4df7-bfa7-be70eb68f04e'
```

### <a name="referencing-a-column-that-appears-in-multiple-tables-and-has-multiple-types"></a>複数のテーブルに出現し、複数の型を持つ列を参照する

次のようにを実行して2つのテーブルを作成したとします。 

```kusto
.create tables 
  Table1 (Level:string, Timestamp:datetime, ProcessId:string),
  Table2 (Level:string, Timestamp:datetime, ProcessId:int64)
```

* 次のクエリはとして実行され `union` ます。

```kusto
find in (Table1, Table2) where ProcessId == 1001
```

出力結果のスキーマは、 *(Level: string、Timestamp、ProcessId_string、ProcessId_int)* になります。

* 次のクエリもとして実行され `union` ますが、異なる結果スキーマが生成されます。

```kusto
find in (Table1, Table2) where ProcessId == 1001 project Level, Timestamp, ProcessId:string 
```

出力結果のスキーマは *(Level: string、Timestamp、ProcessId_string)* になります。
