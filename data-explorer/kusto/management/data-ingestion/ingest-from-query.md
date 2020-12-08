---
title: Kusto のクエリ取り込み (設定、追加、置換) - Azure Data Explorer
description: この記事では、Azure Data Explorer でのクエリからの取り込み (.set、.append、.set-or-append、.set-or-replace) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.localizationpriority: high
ms.openlocfilehash: 18959e2387a1a0faf92261dc3c35eca0db44c158
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512895"
---
# <a name="ingest-from-query-set-append-set-or-append-set-or-replace"></a>クエリからの取り込み (.set、.append、.set-or-append、.set-or-replace)

これらのコマンドを使用すると、クエリまたは制御コマンドを実行し、クエリの結果をテーブルに取り込むことができます。 これらのコマンドの違いは、既存の、または存在しないテーブルとデータの処理方法にあります。

|コマンド          |テーブルが存在する場合                     |テーブルが存在しない場合                    |
|-----------------|------------------------------------|------------------------------------------|
|`.set`           |コマンドは失敗します                  |テーブルが作成され、データは取り込まれます|
|`.append`        |データはテーブルに追加されます      |コマンドは失敗します                        |
|`.set-or-append` |データはテーブルに追加されます      |テーブルが作成され、データは取り込まれます|
|`.set-or-replace`|テーブル内のデータはデータによって置き換えられます|テーブルが作成され、データは取り込まれます|

**構文**

`.set` [`async`] *TableName* [`with` `(`*PropertyName* `=` *PropertyValue* [`,` ...]`)`] `<|` *QueryOrCommand*

`.append` [`async`] *TableName* [`with` `(`*PropertyName* `=` *PropertyValue* [`,` ...`])`] `<|` *QueryOrCommand*

`.set-or-append` [`async`] *TableName* [`with` `(`*PropertyName* `=` *PropertyValue* [`,` ...]`)`] `<|` *QueryOrCommand*

`.set-or-replace` [`async`] *TableName* [`with` `(`*PropertyName* `=` *PropertyValue* [`,` ...]`)`] `<|` *QueryOrCommand*

**引数**

* `async`:指定した場合、コマンドの制御はすぐに戻り、バックグラウンドで取り込みが続行されます。 コマンドの結果には `OperationId` 値が含まれます。これを `.show operations` コマンドに使用して、取り込みの完了状態と結果を取得できます。
* *TableName*:データの取り込み先となるテーブルの名前。
  テーブル名は、コンテキスト内のデータベースに常に関連付けられています。
* *PropertyName*、*PropertyValue*:取り込みプロセスに影響を与える任意の数の取り込みプロパティ。

 サポートされている取り込みプロパティ。

|プロパティ        |[説明]|
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
|`creationTime`   | 取り込まれたデータ エクステントの作成時刻に使用する datetime 値 (ISO8601 文字列の形式)。 指定しない場合は、現在の値 (`now()`) が使用されます。|
|`extend_schema`  | 指定した場合、テーブルのスキーマを拡張するようにコマンドに指示するブール値。 既定値は "false" です。 このオプションは、`.append`、`.set-or-append`、`set-or-replace` コマンドにのみ適用されます。 許容されているスキーマ拡張の場合にのみ、テーブルの最後に列が追加されます。|
|`recreate_schema`  | ブール値。 指定した場合、コマンドによってテーブルのスキーマを再作成できるかどうかを示します。 既定値は "false" です。 このオプションは、*set-or-replace* コマンドにのみ適用されます。 両方が設定されている場合、このオプションは extend_schema プロパティよりも優先されます|
|`folder`         | テーブルに割り当てるフォルダー。 テーブルが既に存在する場合は、このプロパティによりテーブルのフォルダーが上書きされます。|
|`ingestIfNotExists`   | 文字列値。 指定した場合、同じ値を持つ、`ingest-by:` タグでタグが付けられたデータが既にテーブルにある場合、取り込みは成功しなくなります。|
|`policy_ingestiontime`   | ブール値です。 指定した場合、このコマンドによって作成されるテーブルで、[取り込み時間ポリシー](../../management/ingestiontime-policy.md)を有効にするかどうかを示します。 既定値は "true" です|
|`tags`   | 取り込み中に実行する検証を示す JSON 文字列。|
|`docstring`   | テーブルをドキュメント化する文字列|

 コマンドの動作を制御するプロパティ。

|プロパティ        |Type    |[説明]|
|----------------|--------|-----------------------------------------------------------------------------------------------------------------------------|
|`distributed`   |`bool`  |コマンドによって、クエリを並列実行しているすべてのノードから取り込まれることを示します。 既定値は "false" です。  以下の解説を参照してください。|

* *QueryOrCommand*:結果が取り込み対象のデータとして使用される、クエリまたは制御コマンドのテキスト。

> [!NOTE]
> `.show` 制御コマンドのみがサポートされています。

**解説**

* テーブルのデータが存在する場合は、`.set-or-replace` によって置き換えられます。 まだ存在しない場合は、既存のデータ シャードが削除されるか、対象のテーブルが作成されます。
  `extend_schema` または `recreate_schema` の取り込みプロパティのいずれかが "true" に設定されていない限り、テーブル スキーマは保持されます。 スキーマが変更される場合、そのトランザクション内で実際のデータが取り込まれる前に行われます。 データの取り込みに失敗しても、スキーマが変更されていないというわけではありません。
* `.set-or-append` および `.append` コマンドの場合、`extend_schema` 取り込みプロパティが "true" に設定されていない限り、スキーマは保持されます。 スキーマが変更される場合、そのトランザクション内で実際のデータが取り込まれる前に行われます。 データの取り込みに失敗しても、スキーマが変更されていないというわけではありません。
* 取り込み対象のデータは、取り込み操作ごとに 1 GB 未満に制限することをお勧めします。 必要に応じて、複数の取り込みコマンドを使用できます。
* データの取り込みはリソースを大量に消費する操作であり、クエリの実行など、クラスターでの同時実行アクティビティに影響する可能性があります。 このようなコマンドを同時に多数実行することは避けてください。
* 結果セットのスキーマをターゲット テーブルのスキーマと照合する場合、比較は列の型に基づいて行われます。 列名の照合はありません。 クエリ結果のスキーマ列は、テーブルと同じ順序にしてください。 そうしないと、データは間違った列に取り込まれます。
* `distributed` フラグを "true" に設定することは、クエリによって生成されるデータの量が多く (1 GB を超える)、クエリにシリアル化が不要であり、そのため複数のノードから並列で出力を生成できる場合に役立ちます。
  クエリ結果が少量の場合は、このフラグを使用しないでください。不必要に多数の小さなデータ シャードが生成される可能性があります。

**例** 

:::no-loc text="LogsTable"::: と同じスキーマを持ち、過去 1 時間のすべてのエラー レコードを保持する :::no-loc text="RecentErrors"::: という名前の新しいテーブルをデータベースに作成します。

```kusto
.set RecentErrors <|
   LogsTable
   | where Level == "Error" and Timestamp > now() - time(1h)
```

データベースに "OldExtents" という名前の新しいテーブルを作成します。このテーブルには、"extent ID" という 1 つの列があり、30 日よりも前に作成されたデータベース内のすべてのエクステントのエクステント ID が保持されます。 データベースには、"MyExtents" という名前の既存のテーブルがあります。

```kusto
.set async OldExtents <|
   MyExtents 
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

現在のデータベース内の "OldExtents" という名前の既存のテーブルにデータを追加します。このテーブルには、"extent ID" という 1 つの列があり、30 日よりも前に作成されたデータベース内のすべてのエクステントのエクステント ID が保持されます。
"MyExtents" という名前の既存のテーブルに基づいて、タグ `tagA` と `tagB` を使用して新しいエクステントにマークします。

```kusto
.append OldExtents with(tags='["TagA","TagB"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

現在のデータベース内の "OldExtents" テーブルにデータを追加するか、テーブルがまだ存在しない場合は作成します。 新しいエクステントに `ingest-by:myTag` を使用してタグを付けます。 これは、"MyExtents" という名前の既存のテーブルに基づいて、`ingest-by:myTag` を使用してタグ付けされたエクステントがテーブルにまだ存在しない場合にのみ行います。

```kusto
.set-or-append async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <|
   MyExtents
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

現在のデータベース内の "OldExtents" テーブルのデータを置き換えるか、テーブルがまだ存在しない場合は作成します。 新しいエクステントに `ingest-by:myTag` を使用してタグを付けます。

```kusto
.set-or-replace async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

現在のデータベース内の "OldExtents" テーブルにデータを追加します。さらに、作成されたエクステントの作成時刻を過去の特定の日時に設定します。

```kusto
.append async OldExtents with(creationTime='2017-02-13T11:09:36.7992775Z') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

**出力を返す**
 
`.set` または `.append` コマンドによって作成されたエクステントに関する情報を返します。

**出力例**

|ExtentId |OriginalSize |ExtentSize |CompressedSize |IndexSize |RowCount | 
|--|--|--|--|--|--|
|23a05ed6-376d-4119-b1fc-6493bcb05563 |1291 |5882 |1568 |4314 |10 |
