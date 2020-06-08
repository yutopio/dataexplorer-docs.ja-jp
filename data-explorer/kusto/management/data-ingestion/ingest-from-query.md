---
title: Kusto クエリインジェスト (set、append、replace)-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのクエリ (. set,. append,. set-または-append,.) からの取り込みについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: cd9d0f9156387f3a42d41b000aefc9eac0793f9d
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512505"
---
# <a name="ingest-from-query-set-append-set-or-append-set-or-replace"></a>クエリからの取り込み (............................

これらのコマンドは、クエリまたは制御コマンドを実行し、クエリの結果をテーブルに取り込みます。 これらのコマンドの違いは、既存のテーブルとデータをどのように扱うかということです。

|コマンド          |テーブルが存在する場合                     |テーブルが存在しない場合                    |
|-----------------|------------------------------------|------------------------------------------|
|`.set`           |コマンドは失敗します。                  |テーブルが作成され、データは取り込まれたになります。|
|`.append`        |テーブルにデータが追加されます。      |コマンドは失敗します。                        |
|`.set-or-append` |テーブルにデータが追加されます。      |テーブルが作成され、データは取り込まれたになります。|
|`.set-or-replace`|データは、テーブル内のデータに置き換えられます。|テーブルが作成され、データは取り込まれたになります。|

**構文**

`.set`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|`*Queryorcommand*

`.append`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ... `])` ] `<|`*Queryorcommand*

`.set-or-append`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|`*Queryorcommand*

`.set-or-replace`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|`*Queryorcommand*

**引数**

* `async`: 指定した場合、コマンドはすぐに制御を戻し、バックグラウンドで取り込みを続行します。 コマンドの結果には、インジェストの `OperationId` `.show operations` 完了状態と結果を取得するためにコマンドで使用できる値が含まれます。
* *TableName*: データの取り込み先となるテーブルの名前。
  テーブル名は、常にデータベースに対してコンテキストで相対的に指定されます。
* *PropertyName*、 *PropertyValue*: インジェスト処理に影響を与える任意の数のインジェストプロパティ。

 サポートされているインジェストのプロパティ:

|プロパティ        |説明|
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
|`creationTime`   | 取り込まれたデータエクステントの作成時に使用する datetime 値 (文字列形式)。 指定されていない場合は、現在の値 (now ()) が使用されます。|
|`extend_schema`  | ブール値。指定した場合、テーブルのスキーマを拡張するようにコマンドに指示します (既定値は false)。 このオプションは、にのみ適用されます。を追加します。 許可されるスキーマ拡張のみ、テーブルの最後に列が追加されます。|
|`recreate_schema`  | 指定されている場合、コマンドがテーブルのスキーマを再作成できるかどうかを示すブール値 (既定値は false)。 このオプションは、セットまたは置換コマンドにのみ適用されます。 両方が設定されている場合、このオプションは extend_schema プロパティよりも優先されます。|
|`folder`         | テーブルに割り当てるフォルダー。 テーブルが既に存在する場合、このプロパティはテーブルのフォルダーよりも優先されます。|
|`ingestIfNotExists`   | 指定されている場合、同じ値を使用して、インジェストによってタグ付けされたデータをテーブルに既に持っている場合、その文字列値を使用して取り込みを防止できます。|
|`policy_ingestiontime`   | ブール値。指定されている場合、このコマンドで作成されるテーブルで [IngestionTime ポリシー](../../management/ingestiontime-policy.md)を有効にするかどうかを記述します  既定値は true です。|
|`tags`   | インジェスト中に実行する検証を示す JSON 文字列。|
|`docstring`   | テーブルを文書化する文字列。|

  また、コマンド自体の動作を制御するプロパティもあります。

|プロパティ        |Type    |[説明]|
|----------------|--------|-----------------------------------------------------------------------------------------------------------------------------|
|`distributed`   |`bool`  |コマンドが、クエリを並列実行しているすべてのノードから取り込みしたことを示します。 (既定値は `false` です)。 以下の解説を参照してください。|

* *Queryorcommand*: 出力データとして使用されるクエリまたはコントロールコマンドのテキスト。

> [!NOTE]
> `.show`制御コマンドのみがサポートされています。

**解説**

* `.set-or-replace`存在する場合は、テーブルのデータを置き換えます (既存のデータシャードを削除します)。存在しない場合は、対象テーブルを作成します。
  `extend_schema`またはインジェストプロパティのいずれかがに設定されていない限り、テーブルスキーマは保存され `recreate_schema` `true` ます。 スキーマが変更された場合は、実際のデータインジェストが独自のトランザクションで行われる前に発生します。そのため、データを取り込むことができなかった場合は、スキーマが変更されていないということではありません。
* `.set-or-append``.append` `extend_schema` インジェストプロパティがに設定されていない限り、およびコマンドはスキーマを保持し `true` ます。 スキーマが変更された場合は、実際のデータインジェストが独自のトランザクションで行われる前に発生します。そのため、データを取り込むことができなかった場合は、スキーマが変更されていないということではありません。
* インジェストのデータは、インジェスト操作あたり 1 GB 未満に制限することを**強くお勧め**します。 必要に応じて、複数のインジェストコマンドを使用できます。
* データインジェストはリソースを大量に消費する操作であり、クエリの実行など、クラスター上の同時実行アクティビティに影響を与える可能性があります。 複数のコマンドを同時に実行することは避けてください。
* 結果セットのスキーマを対象のテーブルのスキーマと照合する場合、比較は列の型に基づいて行われます。 列名が一致しないので、クエリ結果のスキーマ列がテーブルと同じ順序になっていることを確認してください。そうしないと、データが間違った列に取り込まれたされます。
* `distributed`フラグをに設定すると、 `true` クエリによって生成されるデータの量が大きい (1 gb のデータを超え**ている**) 場合に、クエリでシリアル化が必要ない (複数のノードが並列で出力を生成できるように) 場合に便利です。
  クエリ結果が小さい場合は、このフラグを使用しないことをお勧めします。このフラグは、大量の小さなデータシャードを不必要に生成する可能性があるためです。

**使用例** 

現在のデータベースに "RecentErrors" という名前の新しいテーブルを作成します。このテーブルには、"LogsTable" と同じスキーマがあり、過去1時間のすべてのエラーレコードが保持されます。

```kusto
.set RecentErrors <|
   LogsTable
   | where Level == "Error" and Timestamp > now() - time(1h)
```

現在のデータベースの "OldExtents" という名前の新しいテーブルを作成します。このテーブルには、"MyExtents" という名前の既存のテーブルに基づいて、1つの列 ("ExtentId") があり、データベース内のすべてのエクステントのエクステント Id が30日以上前に作成されています。

```kusto
.set async OldExtents <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

現在のデータベースの "OldExtents" と呼ばれる既存のテーブルにデータを追加します。このテーブルには、1つの列 ("ExtentId") が含まれてい `tagA` `tagB` ます。また、"myextents" という名前の既存のテーブルに基づいて、タグとを使用して新しいエクステントにタグを付けることができます。

```kusto
.append OldExtents with(tags='["TagA","TagB"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```
 
現在のデータベースの "OldExtents" テーブルにデータを追加します (または、テーブルが存在しない場合は作成します)。また、を使用して新しいエクステントにタグを付け `ingest-by:myTag` ます。 これは `ingest-by:myTag` 、"MyExtents" という名前の既存のテーブルに基づいて、テーブルにタグ付けされたエクステントがまだ含まれていない場合にのみ実行します。

```kusto
.set-or-append async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

現在のデータベースの "OldExtents" テーブルのデータを置き換えます (または、テーブルがまだ存在しない場合は作成します)。また、で新しいエクステントにタグを付け `ingest-by:myTag` ます。

```kusto
.set-or-replace async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

現在のデータベースの "OldExtents" テーブルにデータを追加し、作成されたエクステントの作成時刻を過去の特定の日時に設定します。

```kusto
.append async OldExtents with(creationTime='2017-02-13T11:09:36.7992775Z') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId     
```

**出力を返す**
 
またはコマンドの結果として作成されたエクステントに関する情報を返し `.set` `.append` ます。

**出力例**

|ExtentId |OriginalSize |ExtentSize |CompressedSize |IndexSize |RowCount | 
|--|--|--|--|--|--|
|23a05ed6-376d-4119-b1fc-6493bcb05563 |1291 |5882 |1568 |4314 |10 |