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
ms.openlocfilehash: 5248b9d986845ff7f35085cef0100cf3ab4b90da
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264472"
---
# <a name="ingest-from-query-set-append-set-or-append-set-or-replace"></a>クエリからの取り込み (............................

これらのコマンドは、クエリまたは制御コマンドを実行し、クエリの結果をテーブルに取り込みます。 これらのコマンドの違いは、既存のテーブルやデータをどのように扱うかということです。

|コマンド          |テーブルが存在する場合                     |テーブルが存在しない場合                    |
|-----------------|------------------------------------|------------------------------------------|
|`.set`           |コマンドが失敗する                  |テーブルが作成され、データは取り込まれたになります。|
|`.append`        |テーブルにデータが追加されます。      |コマンドが失敗する                        |
|`.set-or-append` |テーブルにデータが追加されます。      |テーブルが作成され、データは取り込まれたになります。|
|`.set-or-replace`|データがテーブル内のデータに置き換えられる|テーブルが作成され、データは取り込まれたになります。|

**構文**

`.set`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|`*Queryorcommand*

`.append`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ... `])` ] `<|`*Queryorcommand*

`.set-or-append`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|`*Queryorcommand*

`.set-or-replace`[ `async` ] *TableName* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|`*Queryorcommand*

**引数**

* `async`: 指定した場合、コマンドはすぐに制御を戻し、バックグラウンドで取り込みを続行します。 コマンドの結果には、インジェストの `OperationId` `.show operations` 完了状態と結果を取得するためにコマンドで使用できる値が含まれます。
* *TableName*: データの取り込み先となるテーブルの名前。
  テーブル名は、常にデータベースに関連付けられています。
* *PropertyName*、 *PropertyValue*: インジェスト処理に影響を与える任意の数のインジェストプロパティ。

 サポートされているインジェストプロパティ。

|プロパティ        |説明|
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
|`creationTime`   | 取り込まれたデータエクステントの作成時に使用する、文字列形式の datetime 値。 指定しない場合、現在の値 ( `now()` ) が使用されます。|
|`extend_schema`  | ブール値。指定した場合、テーブルのスキーマを拡張するようにコマンドに指示します。 既定値は "false" です。 このオプションは、、、およびの各コマンドにのみ適用さ `.append` `.set-or-append` `set-or-replace` れます。 許可されるスキーマ拡張では、最後にテーブルに追加の列が追加されます。|
|`recreate_schema`  | ブール値を指定します。 指定した場合、コマンドがテーブルのスキーマを再作成できるかどうかを示します。 既定値は "false" です。 このオプションは、*セットまたは置換*コマンドにのみ適用されます。 両方が設定されている場合、このオプションは extend_schema プロパティよりも優先されます。|
|`folder`         | テーブルに割り当てるフォルダー。 テーブルが既に存在する場合は、このプロパティによってテーブルのフォルダーが上書きされます。|
|`ingestIfNotExists`   | を表す文字列値。 指定した場合、 `ingest-by:` 同じ値を持つタグのタグが付いたデータが既にテーブルに存在する場合、インジェストは失敗します。|
|`policy_ingestiontime`   | ブール値です。 指定されている場合、このコマンドによって作成されたテーブルの[インジェスト時間ポリシー](../../management/ingestiontime-policy.md)を有効にするかどうかを示します。 既定値は "true" です。|
|`tags`   | インジェスト中に実行する検証を示す JSON 文字列|
|`docstring`   | テーブルをドキュメント化する文字列|

 コマンドの動作を制御するプロパティです。

|プロパティ        |Type    |[説明]|
|----------------|--------|-----------------------------------------------------------------------------------------------------------------------------|
|`distributed`   |`bool`  |コマンドが、クエリを並列実行しているすべてのノードから取り込みしたことを示します。 既定値は "false" です。  次の説明を参照してください。|

* *Queryorcommand*: 出力データとして使用されるクエリまたはコントロールコマンドのテキスト。

> [!NOTE]
> `.show`制御コマンドのみがサポートされています。

**解説**

* `.set-or-replace`テーブルのデータが存在する場合は、そのデータを置き換えます。 既存のデータシャードが削除されるか、または対象のテーブルがまだ存在しない場合は作成されます。
  `extend_schema`またはインジェストプロパティのいずれか `recreate_schema` が "true" に設定されていない限り、テーブルスキーマは保存されます。 スキーマが変更された場合は、実際のデータインジェストの前に独自のトランザクションが発生します。 データの取り込みに失敗した場合、スキーマが変更されていないということではありません。
* `.set-or-append``.append` `extend_schema` インジェストプロパティが "true" に設定されていない限り、およびコマンドはスキーマを保持します。 スキーマが変更された場合は、実際のデータインジェストの前に独自のトランザクションが発生します。 データの取り込みに失敗した場合、スキーマが変更されていないということではありません。
* インジェスト操作あたり 1 GB 未満になるようにデータを制限することをお勧めします。 必要に応じて、複数のインジェストコマンドを使用できます。
* データインジェストはリソースを大量に消費する操作であり、クエリの実行など、クラスター上の同時実行アクティビティに影響を与える可能性があります。 このようなコマンドを同時に実行することは避けてください。
* 結果セットのスキーマを対象のテーブルのスキーマと照合する場合、比較は列の型に基づいて行われます。 列名に一致するものがありません。 クエリ結果のスキーマ列がテーブルと同じ順序になっていることを確認します。 それ以外の場合、データは間違った列に取り込まれたされます。
* フラグを `distributed` "true" に設定すると、クエリによって生成されるデータの量が 1 gb を超える場合や、クエリでシリアル化が必要ない場合に、複数のノードが同時に出力を生成できるようになります。
  クエリ結果が小さい場合は、このフラグを使用しないでください。多くの小さなデータシャードが不必要に生成される可能性があるためです。

**使用例** 

"LogsTable" と同じスキーマを持ち、過去1時間のすべてのエラーレコードを保持する "RecentErrors" という名前の新しいテーブルをデータベースに作成します。

```kusto
.set RecentErrors <|
   LogsTable
   | where Level == "Error" and Timestamp > now() - time(1h)
```

データベース内の "OldExtents" という名前の新しいテーブルを作成します。このテーブルには、"ExtentId" という1つの列があり、データベース内の、30日以上前に作成されたすべてのエクステントのエクステント Id が保持されます。 データベースには、"MyExtents" という名前の既存のテーブルがあります。

```kusto
.set async OldExtents <|
   MyExtents 
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

現在のデータベースの "OldExtents" という名前の既存のテーブルにデータを追加します。このテーブルには、"ExtentId" という1つの列があり、データベース内のすべてのエクステントのエクステント Id が30日以上前に作成されています。
`tagA` `tagB` "Myextents" という名前の既存のテーブルに基づいて、タグとを使用して新しいエクステントをマークします。

```kusto
.append OldExtents with(tags='["TagA","TagB"]') <| 
   MyExtents 
   | where CreatedOn < now() - time(30d) 
   | project ExtentId
```

現在のデータベースの "OldExtents" テーブルにデータを追加するか、テーブルが存在しない場合は作成します。 で新しいエクステントにタグを付け `ingest-by:myTag` ます。 これは `ingest-by:myTag` 、"MyExtents" という名前の既存のテーブルに基づいて、テーブルにというタグが付けられていない場合にのみ実行します。

```kusto
.set-or-append async OldExtents with(tags='["ingest-by:myTag"]', ingestIfNotExists='["myTag"]') <|
   MyExtents
   | where CreatedOn < now() - time(30d)
   | project ExtentId
```

現在のデータベースの "OldExtents" テーブルのデータを置き換えるか、テーブルが存在しない場合は作成します。 で新しいエクステントにタグを付け `ingest-by:myTag` ます。

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
 
またはコマンドによって作成されたエクステントに関する情報を返し `.set` `.append` ます。

**出力例**

|ExtentId |OriginalSize |ExtentSize |CompressedSize |IndexSize |RowCount | 
|--|--|--|--|--|--|
|23a05ed6-376d-4119-b1fc-6493bcb05563 |1291 |5882 |1568 |4314 |10 |
