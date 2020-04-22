---
title: エクステント (データ シャード) 管理 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのエクステント (データ シャード) の管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: e94b830809bf079e612b477292d00d2dbe85e60e
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744732"
---
# <a name="extents-data-shards-management"></a>エクステント (データ・シャード) 管理

データシャードは Kusto の**エクステント**と呼ばれ、すべてのコマンドはシノニムとして「エクステント」または「エクステント」を使用します。

## <a name="show-extents"></a>.表示範囲

**クラスタ レベル**

`.show` `cluster` `extents` [`hot`]

クラスター内に存在するエクステント (データ・シャード) に関する情報を表示します。
指定`hot`されている場合は、ホット・キャッシュ内に存在すると予想されるエクステントのみを表示します。

**データベース レベル**

`.show``database`*データベース名*`extents``(`[*エクステントId1..*`,``,`*エクステントIdN*`)``hot`]`where` `tags` [`has`|`contains`|`!has`|] [`and` `tags` `has`|`contains`|`!has`|`!contains`]`!contains`)*タグ1* [ ( )*タグ2*.]]

指定したデータベースに存在するエクステント (データシャード) に関する情報を表示します。
指定`hot`されている場合は、ホット・キャッシュ内に存在すると想定されるエクステントのみを表示します。

**テーブル レベル**

`.show``table`*テーブル名*`extents``(`[*エクステント Id1*`,`..`,`*エクステントIdN*`)``hot`]`where` `tags` [`has`|`contains`|`!has`|] [`and` `tags` `has`|`contains`|`!has`|`!contains`]`!contains`)*タグ1* [ ( )*タグ2*.]]

`.show``tables``,`テーブル名1 .. *TableName1* `(``,`*テーブル名N* `)` `extents` `(`[*エクステント Id1*`,`..`,`*エクステントIdN*`)``hot`]`where` `tags` [`has`|`contains`|`!has`|] [`and` `tags` `has`|`contains`|`!has`|`!contains`]`!contains`)*タグ1* [ ( )*タグ2*.]]

指定したテーブルに存在するエクステント (データシャード) に関する情報を表示します (データベースはコマンドのコンテキストから取得されます)。
指定`hot`されている場合は、ホット・キャッシュ内に存在すると想定されるエクステントのみを表示します。

**注**

* データベースレベルまたはテーブルレベルのコマンドを使用する方が、クラスタレベルの`| where DatabaseName == '...' and TableName == '...'`コマンドの結果をフィルタリング (追加) するよりもはるかに高速です。
* オプションのエクステント ID のリストが指定されている場合、返されるデータ・セットは、それらのエクステントのみに限定されます。
    * 繰り返しますが、これは「裸」コマンド`| where ExtentId in(...)`の結果をフィルタリング(追加)するよりもはるかに高速です。
* フィルタが`tags`指定されている場合:
  * 返されるリストは、タグ コレクションが提供*されているすべての*タグ フィルターに従うエクステントに制限されます。
    * 繰り返しますが、これは「裸」コマンド`| where Tags has '...' and Tags contains '...'`の結果をフィルタリング(追加)するよりもはるかに高速です。
  * `has`フィルターは等値フィルターで、指定されたタグのいずれかでタグ付けされていないエクステントは除外されます。
  * `!has`フィルターは等値負のフィルターです: 指定されたタグのいずれかでタグ付けされたエクステントは除外されます。
  * `contains`フィルタは、大文字と小文字を区別しない部分文字列フィルタです: タグのサブ文字列として指定された文字列を持たないエクステントは、除外されます。 
  * `!contains`フィルタは、大文字と小文字を区別しない部分文字列の負のフィルタです: 指定された文字列をタグのサブ文字列として持つエクステントは、除外されます。 
  
  * **使用例**
    * テーブル`E``T`内のエクステントには タグ`aaa``BBB`と`ccc`がタグ付けされます。
    * このクエリは、`E`を返します。 
    
    ```kusto 
    .show table T extents where tags has 'aaa' and tags contains 'bb' 
    ``` 
    
    * このクエリは*not*返`E`されません (タグ付けされていないので、次のようになります`aa`):
    
    ```kusto 
    .show table T extents where tags has 'aa' and tags contains 'bb' 
    ``` 
    
    * このクエリは、`E`を返します。 
    
    ```kusto 
    .show table T extents where tags contains 'aaa' and tags contains 'bb' 
    ``` 

|出力パラメーター |Type |説明 
|---|---|---
|エクステントID |Guid |範囲の ID 
|DatabaseName |String |エクステントが属するデータベース 
|TableName |String |エクステントが属するテーブル 
|マックスクリズオン |DateTime |エクステントが作成された日時 (マージされたエクステントの場合 - ソースエクステント間の作成時間の最大値) 
|オリジナルサイズ |Double |エクステント データの元のサイズ (バイト単位) 
|エクステントサイズ |Double |メモリ内のエクステントのサイズ (圧縮 + インデックス) 
|圧縮サイズ |Double |メモリ内のエクステント・データの圧縮サイズ 
|IndexSize |Double |エクステント・データの索引サイズ 
|Blocks |Long |エクステント内のデータ・ブロックの量 
|セグメント |Long |エクステント内のデータ・セグメントの量 
|割り当てられたデータノード |String | 非推奨 (空の文字列)
|読み込まれたデータノード |String |非推奨 (空の文字列)
|エクステントコンテナ Id |String | エクステントが含まれるエクステント コンテナの ID
|RowCount |Long |エクステント内の行の量
|ミンCreatedOn |DateTime |エクステントが作成された日時 (マージされたエクステントの場合 - ソースエクステント間の作成時間の最小値) 
|Tags|String|エクステントに定義されているタグ (存在する場合) 
 
**使用例**

```kusto 
// Show volume of extents being created per hour in a specific database
.show database MyDatabase extents | summarize count(ExtentId) by MaxCreatedOn bin=time(1h) | render timechart  

// Show volume of data arriving by table per hour 
.show database MyDatabase extents  
| summarize sum(OriginalSize) by TableName, MaxCreatedOn bin=time(1h)  
| render timechart

// Show data size distribution by table 
.show database MyDatabase extents | summarize sum(OriginalSize) by TableName

// Show all extents in the database named 'GamesDB' 
.show database GamesDB extents

// Show all extents in the table named 'Games' 
.show table Games extents

// Show all extents in the tables named 'TaggingGames1' and 'TaggingGames2', tagged with both 'tag1' and 'tag2' 
.show tables (TaggingGames1,TaggingGames2) extents where tags has 'tag1' and tags has 'tag2'
``` 
 
## <a name="merge-extents"></a>.マージエクステント

**構文**

`.merge``[async | dryrun]`*テーブル名*`(` *GUID1* [`,` *GUID2* .]]`)``[with(rebuild=true)]`

このコマンドは、指定された表の ID によって示されるエクステント (参照:[エクステント (データ・シャード) の概要](extents-overview.md)) をマージします。

マージ操作には、*マージ*(インデックスを再構築する) と*再構築*(データを完全に再び使用する) という 2 つのフレーバーがあります。

* `async`: 操作は非同期的に実行されます - 結果は、コマンドの状態&状態を表示するために`.show operations <operation ID>`実行できる操作 ID (GUID) になります。
* `dryrun`: この操作では、マージする必要があるエクステントのみがリストされますが、実際にはそれらをマージしません。 
* `with(rebuild=true)`: エクステントはマージされる代わりに再構築されます (データは再生成されます)。

**出力を返す**

出力パラメーター |Type |説明 
---|---|---
オリジナルエクステントId |string |ソース テーブル内の元のエクステントの一意識別子 (GUID) です。 
結果の範囲 Id |string |ソース範囲から作成された結果の範囲の一意識別子 (GUID)。 失敗時 - "失敗" または "破棄" 。
Duration |TimeSpan |マージ操作の完了にかかった期間。

**使用例**

で 2 つの特定の`MyTable`エクステントを再構築します。
```kusto
.merge async MyTable (e133f050-a1e2-4dad-8552-1f5cf47cab69, 0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687) with(rebuild=true)
```

で 2 つの特定の`MyTable`エクステントをマージします。
```kusto
.merge MyTable (12345050-a1e2-4dad-8552-1f5cf47cab69, 98765b2d-9dd2-4d2c-a45e-b24c65aa6687)
```

**メモ**
- 一般に`.merge`、コマンドは手動で実行することはほとんどなく、Kusto クラスタのバックグラウンドで自動的に実行されます (その中のテーブルとデータベースに定義されている[マージ ポリシー](mergepolicy.md)に従います)。  
  - 複数のエクステントを 1 つのエクステントにマージする際の一般的な考慮事項については、[マージ ポリシー](mergepolicy.md)に関する説明を参照してください。
- `.merge`操作は`Abandoned`(操作 ID で実行`.show operations`するときに見ることができる)の可能な最終的な状態を持つ - この状態は、ソースエクステントの一部がソーステーブルに存在しなくなったため、ソースエクステントがマージされなくなったことを示唆しています。
  - このような状態は、次のような場合に発生すると予想されます。
     - ソースエクステントは、テーブルの保持の一部として削除されました。
     - ソースエクステントが別のテーブルに移動されました。
     - ソース テーブルが完全に削除または名前変更されました。

## <a name="move-extents"></a>移動範囲

**構文**

`.move`[`async` `extents` `all` `from` ]`table`*ソース テーブル名*`to``table`*の変換先テーブル名*

`.move`]`async` `extents` `(` ] *GUID1* [`,` *GUID2* .])`)``table`*DestinationTableName**SourceTableName*変換先`to`テーブル名`from``table` 

`.move`[`async` `extents` `to` `table` ]*宛先テーブル名* <| *クエリ*

このコマンドは、特定のデータベースのコンテキストで実行され、指定されたエクステントをソース テーブルから転送先テーブルにトランザクション的に移動します。
変換元テーブルと変換先テーブルに対するテーブル[管理者権限](../management/access-control/role-based-authorization.md)が必要です。

* `async`(オプション)は、コマンドが非同期に実行されるかどうかを指定します (この場合、操作 ID (Guid) が返され、操作の状態を[.show operation](operations.md#show-operations)コマンドを使用して監視できます)。
    * このオプションを使用した場合、実行が成功した結果を[取得できます。.show 操作の詳細](operations.md#show-operation-details)コマンド)。

移動するエクステントを指定するには、次の 3 つの方法があります。
1. 特定のテーブルのすべてのエクステントを移動します。
2. ソース表のエクステント ID を明示的に指定する。
3. ソース テーブルのエクステント ID を指定する結果を持つクエリを提供する。

**制限事項**
- ソース テーブルと変換先テーブルの両方がコンテキスト データベースに含まれる必要があります。 
- 変換元テーブルのすべての列は、同じ名前とデータ型の変換先テーブルに存在することが想定されます。

**クエリを使用したエクステントの指定**

```kusto 
.move extents to table TableName <| ...query... 
```

範囲は、"ExtentId" という列を持つレコードセットを返す Kusto クエリを使用して指定します。 

**出力を返す**(同期実行用)

出力パラメーター |Type |説明 
---|---|---
オリジナルエクステントId |string |変換元テーブルの元のエクステントの一意識別子 (GUID) です。 
結果の範囲 Id |string |ソース テーブルから変換先テーブルに移動された結果の範囲の一意識別子 (GUID)。 失敗時 - "失敗"。
詳細 |string |操作が失敗した場合のエラーの詳細を含めます。

**使用例**

テーブル内のすべてのエクステントを`MyTable`テーブル`MyOtherTable`に移動します。
```kusto
.move extents all from table MyTable to table MyOtherTable
```

2 つの特定のエクステントをテーブルからテーブル`MyTable``MyOtherTable`に移動します ( エクステント ID によって ) 。
```kusto
.move extents (AE6CD250-BE62-4978-90F2-5CB7A10D16D7,399F9254-4751-49E3-8192-C1CA78020706) from table MyTable to table MyOtherTable
```

2 つの特定のテーブル (`MyTable1`, `MyTable2`)`MyOtherTable`からすべてのエクステントをテーブル に移動します。
```kusto
.move extents to table MyOtherTable <| .show tables (MyTable1,MyTable2) extents
```

**出力例** 

|オリジナルエクステントId |結果の範囲 Id| 詳細
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 

## <a name="drop-extents"></a>.ドロップエクステント

指定したデータベース/テーブルからエクステントを削除します。 このコマンドには複数のバリアントがあります: 1 つのバリアントでは、削除するエクステントは Kusto クエリで指定されますが、他のバリアントではエクステントは以下に説明するミニ言語を使用して指定されます。 
 
### <a name="specifying-extents-with-a-query"></a>クエリを使用したエクステントの指定

指定されたクエリによって返されるエクステントを持つテーブルのそれぞれに対して、[テーブル管理権限](../management/access-control/role-based-authorization.md)が必要です。

エクステントをドロップします (使用されている場合`whatif`は実際にドロップせずに報告するだけです)。

**構文**

`.drop``extents` ]`whatif`] <|*クエリ*

範囲は、"ExtentId" という列を持つレコードセットを返す Kusto クエリを使用して指定します。 
 
### <a name="dropping-a-specific-extent"></a>特定のエクステントのドロップ

テーブル名が指定されている場合に、[テーブル管理者権限](../management/access-control/role-based-authorization.md)が必要です。

テーブル名が指定されていない場合に備えて[、データベース管理者の権限](../management/access-control/role-based-authorization.md)が必要です。

**構文**

`.drop``extent`*エクステント ID* `from` *[ テーブル名*]

### <a name="dropping-specific-multiple-extents"></a>特定の複数のエクステントのドロップ

テーブル名が指定されている場合に、[テーブル管理者権限](../management/access-control/role-based-authorization.md)が必要です。

テーブル名が指定されていない場合に備えて[、データベース管理者の権限](../management/access-control/role-based-authorization.md)が必要です。

**構文**

`.drop``extents``,`エクステントId1 .. *ExtentId1* `(`*エクステントIdN* `)` `from` *[ テーブル名*]

### <a name="specifying-extents-by-properties"></a>プロパティによる範囲の指定

テーブル名が指定されている場合に、[テーブル管理者権限](../management/access-control/role-based-authorization.md)が必要です。

テーブル名が指定されていない場合に備えて[、データベース管理者の権限](../management/access-control/role-based-authorization.md)が必要です。

`.drop``extents` [`older` *N* N`days` | `hours` `from` ( )] [*テーブル名* | `all``tables``MB` | `GB` | `bytes`]`limit` [`trim` `by` `extentsize` | `datasize` *]* ) N ( [ ] [*制限数*]

* `older`: *N*日/時間より古いエクステントのみが削除されます。 
* `trim`: この操作では、エクステントの合計が目的のサイズ (MaxSize) に一致するまで、データベース内のデータがトリミングされます。 
* `limit`: 操作は最初の*LimitCount*エクステントに適用されます 

このコマンドはエミュレーション`.drop-pretend`()`.drop`モードをサポートしており、コマンドが実行されるかのように出力されますが、実際には実行されません。

**使用例**

データベース内のすべてのテーブルから 10 日以上前に作成されたすべてのエクステントを削除します。`MyDatabase`

```kusto
.drop extents <| .show database MyDatabase extents | where CreatedOn < now() - time(10d)
```

作成時間が 10`Table1`日`Table2`を超えたテーブルおよび 内のすべてのエクステントを削除します。

```kusto
.drop extents older 10 days from tables (Table1, Table2)
```

エミュレーション・モード: コマンドによって除去されるエクステントを示します (エクステント ID パラメーターはこのコマンドには適用できません)。

```kusto
.drop-pretend extents older 10 days from all tables
```

'TestTable' からすべてのエクステントを削除します。

```kusto
.drop extents from TestTable
```
 
**出力を返す**

|出力パラメーター |Type |説明 
|---|---|---
|エクステントID |String |コマンドの結果としてドロップされたエクステント ID 
|TableName |String |テーブル名 (エクステントが属する場所)  
|作成されたオン |DateTime |エクステントが最初に作成された時期に関する情報を保持するタイム・スタンプ 
 
**出力例** 

|エクステント ID |テーブル名 |作成日時 
|---|---|---
|43c6e03f-1713-4ca7-a52a-5db8a4e8b87d |TestTable |2015-01-12 12:48:49.4298178 

## <a name="replace-extents"></a>.範囲の置換

**構文**

`.replace`[`async` `extents` `in` `table` ] テーブル*に移動するエクステントのテーブル*`},{`クエリから削除する*エクステントの* *DestinationTableName*`<| 
{`クエリ`}`

このコマンドは、特定のデータベースのコンテキストで実行され、指定したエクステントをソース テーブルから変換先テーブルに移動し、指定したエクステントを変換先テーブルから削除します。
すべてのドロップ操作と移動操作は、1 つのトランザクションで実行されます。

変換元テーブルと変換先テーブルに対するテーブル[管理者権限](../management/access-control/role-based-authorization.md)が必要です。

* `async`(オプション)は、コマンドが非同期に実行されるかどうかを指定します (この場合、操作 ID (Guid) が返され、操作の状態を[.show operation](operations.md#show-operations)コマンドを使用して監視できます)。
    * このオプションを使用した場合、実行が成功した結果を[取得できます。.show 操作の詳細](operations.md#show-operation-details)コマンド)。

ドロップまたは移動するエクステントの指定は、2 つのクエリを提供することによって行われます。
- *テーブルから削除するエクステントのクエリ*- このクエリの結果はエクステント ID を指定します  
変換先テーブルから削除する必要があります。
- *テーブルに移動するエクステントのクエリ*- このクエリの結果は、変換先テーブルに移動するソース テーブルのエクステント ID を指定します。

どちらのクエリも、"ExtentId" という列を持つレコードセットを返す必要があります。

**制限事項**
- ソース テーブルと変換先テーブルの両方がコンテキスト データベースに含まれる必要があります。 
- *テーブルから削除されるエクステントのクエリ*で指定されたすべてのエクステントは、変換先テーブルに属していると想定されます。
- 変換元テーブルのすべての列は、同じ名前とデータ型の変換先テーブルに存在することが想定されます。

**出力を返す**(同期実行用)

出力パラメーター |Type |説明 
---|---|---
オリジナルエクステントId |string |移動先テーブルに移動された元のエクステントの一意識別子 (GUID) または - コピー先テーブル内のエクステント (削除された)。
結果の範囲 Id |string |コピー元テーブルから変換先テーブルに移動された結果のエクステントの一意識別子 (GUID) 。 失敗時 - "失敗"。
詳細 |string |操作が失敗した場合のエラーの詳細を含めます。

**使用例**

2`MyTable1`つの特定のテーブル ( , `MyTable2`)`MyOtherTable`から table にすべてのエクス`MyOtherTable`テントを移動`drop-by:MyTag`し、 でタグ付けされたすべてのエクステントを削除します。

```kusto
.replace extents in table MyOtherTable <|
    {.show table MyOtherTable extents where tags has 'drop-by:MyTag'},
    {.show tables (MyTable1,MyTable2) extents}
```

**出力例** 

|オリジナルエクステントId |結果の範囲 Id |詳細
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df| 

*注*: 
> [!NOTE]
> テーブル クエリ*から削除するエクステント*が返すエクステントが、変換先テーブルに存在しない場合、コマンドは失敗します。 これは、置換コマンドが実行される前にエクステントがマージされた場合に発生することがあります。 不足しているエクステントでコマンドが失敗することを確認するには、クエリが期待される ExtentId を返すことを確認します。 以下#1例は、削除するエクステントが MyOtherTable テーブルに存在しない場合は失敗します。 ただし、#2例は、削除するクエリがエクステント ID を返さなかったため、削除する範囲が存在しない場合でも成功します。 

例 1: 

```kusto
.replace extents in table MyOtherTable <|
     { datatable(ExtentId:guid)[ "2cca5844-8f0d-454e-bdad-299e978be5df"] }, { .show table MyTable1 extents }
```

例 2:

```kusto
.replace extents in table MyOtherTable  <|
     { .show table MyOtherTable extents | where ExtentId == guid(2cca5844-8f0d-454e-bdad-299e978be5df) }, { .show table MyTable1 extents }
```



## <a name="drop-extent-tags"></a>.drop 範囲タグ

**構文**

`.drop`[`async` `extent` `tags` `from` ]`table`*テーブル名*`(`'`,`*タグ1*'] '*タグ2*'`,`.`,`'*タグン*']`)`

`.drop`[`async` `extent` `tags`  <| ]*クエリ*

* `async`(オプション)は、コマンドが非同期に実行されるかどうかを指定します (この場合、操作 ID (Guid) が返され、操作の状態を[.show operation](operations.md#show-operations)コマンドを使用して監視できます)。
    * このオプションを使用した場合、実行が成功した結果を[取得できます。.show 操作の詳細](operations.md#show-operation-details)コマンド)。

このコマンドは、特定のデータベースのコンテキストで実行され、指定されたデータベースおよびテーブル内の任意のエクステント (タグを含む) から指定された[エクステント タグ](extents-overview.md#extent-tagging)を削除します。  

どのエクステントからどのタグを削除するかを指定するには、次の 2 つの方法があります。

1. 指定したテーブル内のすべてのエクステントから削除するタグを明示的に指定する。
2. テーブルおよび foreach エクステントの範囲 ID を指定する結果を持つクエリを提供することで、削除する必要があるタグ。

**制限事項**
- すべてのエクステントはコンテキスト・データベース内にあり、同じ表に属している必要があります。 

**クエリを使用したエクステントの指定**

関連するすべての変換元テーブルおよび変換先テーブルに対して、[テーブル管理者権限](../management/access-control/role-based-authorization.md)が必要です。

```kusto 
.drop extent tags <| ...query... 
```

削除する範囲とタグは、"ExtentId" という列と "Tags" という列を持つレコードセットを返す Kusto クエリを使用して指定します。 

*メモ*: [Kusto .NET クライアント ライブラリ](../api/netfx/about-kusto-data.md)を使用する場合は、次の方法を使用して目的のコマンドを生成できます。
- `CslCommandGenerator.GenerateExtentTagsDropByRegexCommand(string tableName, string regex)`
- `CslCommandGenerator.GenerateExtentTagsDropBySubstringCommand(string tableName, string substring)`

**出力を返す**

出力パラメーター |Type |説明 
---|---|---
オリジナルエクステントId |string |タグが変更された (および操作の一部として削除された) 元のエクステントの一意識別子 (GUID) 
結果の範囲 Id |string |タグを変更した (および、操作の一部として作成され、追加された) 結果の範囲の一意識別子 (GUID)。 失敗時 - "失敗"。
結果範囲タグ |string |結果のエクステントにタグ付けされるタグのコレクション (残っている場合)、または操作が失敗した場合は「null」。
詳細 |string |操作が失敗した場合のエラーの詳細を含めます。

**使用例**

タグ付`drop-by:Partition000`けされたテーブル`MyOtherTable`内の任意のエクステントからタグを削除します。

```kusto
.drop extent tags from table MyOtherTable ('drop-by:Partition000')
```

タグ、、`drop-by:20160810104500``a random tag`または、いずれかのタグ付`drop-by:20160810`けされたテーブル`My Table`内の任意の範囲から、または、いずれかを削除します。

```kusto
.drop extent tags from table [My Table] ('drop-by:20160810104500','a random tag','drop-by:20160810')
```

テーブル`MyTable`内`drop-by`のエクステントからすべてのタグを削除します。

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n') 
  | mv-expand Tags to typeof(string)
  | where Tags startswith 'drop-by'
```

テーブル`MyTable`内のエクステントから`drop-by:StreamCreationTime_20160915(\d{6})`正規表現に一致するすべてのタグを削除します。

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n')
  | mv-expand Tags to typeof(string)
  | where Tags matches regex @"drop-by:StreamCreationTime_20160915(\d{6})"
```

**出力例** 

|オリジナルエクステントId |結果の範囲 Id | 結果範囲タグ | 詳細
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | パーティション001 |
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | |
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f | パーティション001 パーティション002 |
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | |

## <a name="alter-extent-tags"></a>.変更範囲タグ

**構文**

`.alter`[`async` `extent` `tags` `(`] ' '`,`*タグ1*'[ '*タグ2*'..`,``,`'*タグN*`)` <| ']*クエリ*

このコマンドは、特定のデータベースのコンテキストで実行され、指定されたクエリによって返されたすべての[エクステントのエクステント タグ](extents-overview.md#extent-tagging)を、指定されたタグセットに変更します。

変更するエクステントとタグは、"ExtentId" という列を持つレコードセットを返す Kusto クエリを使用して指定します。

* `async`(オプション)は、コマンドが非同期に実行されるかどうかを指定します (この場合、操作 ID (Guid) が返され、操作の状態を[.show operation](operations.md#show-operations)コマンドを使用して監視できます)。
    * このオプションを使用した場合、実行が成功した結果を[取得できます。.show 操作の詳細](operations.md#show-operation-details)コマンド)。

関連するすべての[テーブルに対して、テーブル管理権限](../management/access-control/role-based-authorization.md)が必要です。

**制限事項**
- すべてのエクステントはコンテキスト・データベース内にあり、同じ表に属している必要があります。 

**出力を返す**

出力パラメーター |Type |説明 
---|---|---
オリジナルエクステントId |string |タグが変更された (および操作の一部として削除された) 元のエクステントの一意識別子 (GUID) 
結果の範囲 Id |string |タグを変更した (および、操作の一部として作成され、追加された) 結果の範囲の一意識別子 (GUID)。 失敗時 - "失敗"。
結果範囲タグ |string |結果のエクステントにタグ付けされるタグのコレクション、または操作が失敗した場合は "null" です。
詳細 |string |操作が失敗した場合のエラーの詳細を含めます。

**使用例**

テーブル`MyTable`内のすべてのエクステントのタグを`MyTag`に変更します。

```kusto
.alter extent tags ('MyTag') <| .show table MyTable extents
```

でタグ付`MyTable`けされたテーブル内のすべてのエクステントのタグ`drop-by:MyTag``drop-by:MyNewTag`を変更します。 `MyOtherNewTag`

```kusto
.alter extent tags ('drop-by:MyNewTag','MyOtherNewTag') <| .show table MyTable extents where tags has 'drop-by:MyTag'
```

**出力例** 

|オリジナルエクステントId |結果の範囲 Id | 結果範囲タグ | 詳細
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | ドロップバイ:マイニュータグマイその他の新しいタグ| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | ドロップバイ:マイニュータグマイその他の新しいタグ| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42187872f | ドロップバイ:マイニュータグマイその他の新しいタグ| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | ドロップバイ:マイニュータグマイその他の新しいタグ| 

