---
title: エクステント (データシャード) の管理-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでのエクステント (データシャード) の管理について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: ca66beaad41796dff38a5bd5ce1fad2e0f41fc72
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550505"
---
# <a name="extents-data-shards-management"></a>エクステント (データシャード) の管理

データシャードは Kusto では**エクステント**と呼ばれ、すべてのコマンドはシノニムとして "エクステント" または "エクステント" を使用します。

## <a name="show-extents"></a>。エクステントを表示します

**クラスターレベル**

`.show` `cluster` `extents` [`hot`]

クラスター内に存在するエクステント (データシャード) に関する情報を表示します。
`hot`を指定した場合-ホットキャッシュ内に存在することが想定されているエクステントのみが表示されます。

**データベースレベル**

`.show``database` *DatabaseName* `extents` [ `(` *ExtentId1* `,` ... `,`*Extentidn* `)` ][ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*...]]

指定されたデータベースに存在するエクステント (データシャード) に関する情報を表示します。
`hot`が指定されている場合-ホットキャッシュ内に存在することが予想されるエクステントのみを表示します。

**テーブル レベル**

`.show``table` *TableName* `extents` [ `(` *ExtentId1* `,` ... `,`*Extentidn* `)` ][ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*...]]

`.show``tables` `(` *TableName1* `,` ... `,`*TableNameN* `)``extents`[ `(` *ExtentId1* `,` ... `,`*Extentidn* `)` ][ `hot` ] [ `where` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag1* [ `and` `tags` ( `has` | `contains` | `!has` | `!contains` ) *Tag2*...]]

指定されたテーブルに存在するエクステント (データシャード) に関する情報を表示します (データベースはコマンドのコンテキストから取得されます)。
`hot`が指定されている場合-ホットキャッシュ内に存在することが予想されるエクステントのみを表示します。

**注**

* データベースやテーブルレベルのコマンドを使用すると、 `| where DatabaseName == '...' and TableName == '...'` クラスターレベルのコマンドの結果をフィルター処理 (追加) するよりもはるかに高速になります。
* エクステント Id のオプションのリストが指定されている場合、返されるデータセットはそれらのエクステントのみに制限されます。
    * ここでも、 `| where ExtentId in(...)` "ベア" コマンドの結果をフィルター処理する (追加する) よりもはるかに高速です。
* `tags`フィルターが指定されている場合:
  * 返される一覧は、タグコレクションが、指定された*すべて*のタグフィルターに従うエクステントに限定されます。
    * ここでも、 `| where Tags has '...' and Tags contains '...'` "ベア" コマンドの結果をフィルター処理する (追加する) よりもはるかに高速です。
  * `has`フィルターは等値フィルターです。指定されたタグのいずれかにタグ付けされていないエクステントはフィルターで除外されます。
  * `!has`フィルターは等しい負のフィルターです。指定されたタグのいずれかでタグ付けされているエクステントはフィルターで除外されます。
  * `contains`フィルターは大文字と小文字を区別しない部分文字列フィルターです。指定された文字列がタグのいずれかの部分文字列として指定されていないエクステントはフィルターで除外されます。 
  * `!contains`フィルターは大文字と小文字を区別しない部分文字列負のフィルターです。指定された文字列がいずれかのタグの部分文字列として含まれているエクステントはフィルターで除外されます。 
  
  * **例**
    * `E`テーブル内のエクステントに `T` は、タグ、およびタグが付けられ `aaa` `BBB` `ccc` ます。
    * このクエリは次を返し `E` ます。 
    
    ```kusto 
    .show table T extents where tags has 'aaa' and tags contains 'bb' 
    ``` 
    
    * このクエリはを返し*ません*(これはと同じタグでタグ付けされ `E` ないため `aa` )。
    
    ```kusto 
    .show table T extents where tags has 'aa' and tags contains 'bb' 
    ``` 
    
    * このクエリは次を返し `E` ます。 
    
    ```kusto 
    .show table T extents where tags contains 'aaa' and tags contains 'bb' 
    ``` 

|出力パラメーター |種類 |説明 
|---|---|---
|ExtentId |Guid |エクステントの ID 
|DatabaseName |String |エクステントが属しているデータベース 
|TableName |String |エクステントが属しているテーブル 
|MaxCreatedOn |DateTime |エクステントが作成された日時 (マージされたエクステントの場合-ソースエクステント間の最大作成時間) 
|OriginalSize |Double |エクステントデータの元のサイズ (バイト単位) 
|ExtentSize |Double |メモリ内のエクステントのサイズ (圧縮 + インデックス) 
|CompressedSize |Double |メモリ内のエクステントデータの圧縮サイズ 
|IndexSize |Double |エクステントデータのインデックスサイズ 
|ブロック |Long |エクステントのデータブロックの量 
|セグメント |Long |データセグメントの量 (エクステント単位) 
|AssignedDataNodes |String | 非推奨 (空の文字列)
|読み込まれる Datの Des |String |非推奨 (空の文字列)
|ExtentContainerId |String | エクステントが含まれているエクステントコンテナーの ID
|RowCount |Long |エクステント内の行の量
|MinCreatedOn |DateTime |エクステントが作成された日時 (マージされたエクステントの場合-ソースエクステント間の最小作成時間) 
|タグ|String|範囲に対して定義されているタグ (存在する場合) 
 
**例**

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
 
## <a name="merge-extents"></a>。エクステントをマージします。

**構文**

`.merge``[async | dryrun]` *TableName* `(` *GUID1* [ `,` *GUID2* ...] `)``[with(rebuild=true)]`

このコマンドは、指定されたテーブルの Id で示されるエクステント (「[エクステント (データシャード) の概要](extents-overview.md)」を参照) をマージします。

マージ操作には2つのフレーバー (インデックスを再構築する*マージ*) と*リビルド*(データを完全に reingests) があります。

* `async`: 操作は非同期的に実行されます。結果は操作 ID (GUID) になります。この ID は、 `.show operations <operation ID>` コマンドの状態 & 状態を表示するために、で実行できます。
* `dryrun`: この操作では、マージする必要があるエクステントだけが一覧表示されますが、実際にマージされることはありません。 
* `with(rebuild=true)`: エクステントは、マージされるのではなく (データは reingested) 再構築されます (インデックスは再構築されます)。

**出力を返す**

出力パラメーター |種類 |説明 
---|---|---
OriginalExtentId |string |ソーステーブル内の元のエクステントの一意識別子 (GUID)。ターゲットエクステントにマージされています。 
ResultExtentId |string |ソースエクステントから作成された結果エクステントの一意識別子 (GUID)。 失敗時-"失敗" または "破棄済み"。
Duration |TimeSpan |マージ操作を完了するために要した期間。

**例**

で2つの特定のエクステントを再構築 `MyTable` し、非同期的に実行します。
```kusto
.merge async MyTable (e133f050-a1e2-4dad-8552-1f5cf47cab69, 0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687) with(rebuild=true)
```

で2つの特定のエクステント `MyTable` をマージし、同期的に実行します。
```kusto
.merge MyTable (12345050-a1e2-4dad-8552-1f5cf47cab69, 98765b2d-9dd2-4d2c-a45e-b24c65aa6687)
```

**メモ**
- 一般に、 `.merge` コマンドを手動で実行することはめったにありません。コマンドは、Kusto クラスターのバックグラウンドで自動的に実行されます (テーブルおよびデータベースに対して定義されている[マージポリシー](mergepolicy.md)によって異なります)。  
  - 複数のエクステントを1つにマージするための条件に関する一般的な考慮事項については、「[マージポリシー](mergepolicy.md)」で説明しています。
- `.merge`操作には、 `Abandoned` (操作 ID を指定して実行すると表示される) の最終的な状態が考えら `.show operations` れます。この状態では、ソースエクステントの一部がソーステーブルに存在しなくなったため、ソースエクステントが一緒にマージされていないことがわかります。
  - このような状態は、次のような場合に発生します。
     - ソースエクステントはテーブルの保有期間の一部として削除されています。
     - ソースエクステントが別のテーブルに移動されました。
     - ソーステーブルが完全に削除されたか、名前が変更されました。

## <a name="move-extents"></a>。エクステントを移動します。

**構文**

`.move`[ `async` ] `extents` `all` `from` `table` *Sourcetablename* `to` `table` *destinationtablename*

`.move`[ `async` ] `extents` `(` *GUID1* [ `,` *GUID2* ...] `)` `from` `table`*Sourcetablename* `to``table` *Destinationtablename* 

`.move`[ `async` ] `extents` `to` `table` *Destinationtablename*  <|  *クエリ*

このコマンドは、特定のデータベースのコンテキストで実行され、指定されたエクステントをソーステーブルからターゲットテーブルにトランザクションで移動します。
コピー元およびコピー先のテーブルに対する[Table admin 権限](../management/access-control/role-based-authorization.md)が必要です。

* `async`(省略可能) コマンドを非同期的に実行するかどうかを指定します (この場合、操作 ID (Guid) が返され[ます。](operations.md#show-operations)操作の状態は、[操作の表示] コマンドを使用して監視できます)。
    * このオプションが使用されている場合は、正常に実行された結果を取得でき[ます。 [操作の詳細の表示](operations.md#show-operation-details)] コマンド)。

移動するエクステントを指定するには、次の3つの方法があります。
1. 特定のテーブルのすべてのエクステントが移動されます。
2. ソーステーブルのエクステント Id を明示的に指定する。
3. 結果がソーステーブルのエクステント Id を指定するクエリを指定します。

**制限事項**
- ソーステーブルとターゲットテーブルは両方ともコンテキストデータベースに存在する必要があります。 
- コピー元テーブルのすべての列は、同じ名前とデータ型の変換先テーブルに存在することが想定されています。

**クエリを使用したエクステントの指定**

```kusto 
.move extents to table TableName <| ...query... 
```

エクステントは、"ExtentId" という名前の列を含むレコードセットを返す Kusto クエリを使用して指定されます。 

**出力を返す**(同期実行の場合)

出力パラメーター |種類 |説明 
---|---|---
OriginalExtentId |string |コピー先のテーブルに移動された、ソーステーブル内の元のエクステントの一意識別子 (GUID)。 
ResultExtentId |string |ソーステーブルから変換先テーブルに移動された結果エクステントの一意識別子 (GUID)。 失敗時-"失敗"。
詳細 |string |操作が失敗した場合のエラーの詳細が含まれます。

**例**

テーブル内のすべてのエクステント `MyTable` をテーブルに移動 `MyOtherTable` します。
```kusto
.move extents all from table MyTable to table MyOtherTable
```

2つの特定のエクステント (エクステント Id によって) をテーブルから `MyTable` テーブルに移動 `MyOtherTable` します。
```kusto
.move extents (AE6CD250-BE62-4978-90F2-5CB7A10D16D7,399F9254-4751-49E3-8192-C1CA78020706) from table MyTable to table MyOtherTable
```

2つの特定のテーブル (、) のすべてのエクステント `MyTable1` `MyTable2` をテーブルに移動 `MyOtherTable` します。
```kusto
.move extents to table MyOtherTable <| .show tables (MyTable1,MyTable2) extents
```

**出力例** 

|OriginalExtentId |ResultExtentId| 詳細
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42 187872f| 
|2dfdef64-62a3-4950-a13096b5b1083b5a |0fbの 3da-5e2847 f09-a000-e62eb41592df| 

## <a name="drop-extents"></a>。エクステントを削除します。

指定されたデータベースまたはテーブルからエクステントを削除します。 このコマンドにはいくつかのバリエーションがあります。1つのバリアントでは、削除するエクステントは Kusto クエリによって指定されますが、その他のバリアントエクステントは次に示すミニ言語を使用して指定されます。 
 
### <a name="specifying-extents-with-a-query"></a>クエリを使用したエクステントの指定

指定されたクエリによって返されるエクステントを含むテーブルの foreach[権限](../management/access-control/role-based-authorization.md)が必要です。

エクステントを削除します (が使用されている場合は、実際に削除せずにレポート `whatif` するだけです)。

**構文**

`.drop``extents`[ `whatif` ] <|*クエリ*

エクステントは、"ExtentId" という名前の列を含むレコードセットを返す Kusto クエリを使用して指定されます。 
 
### <a name="dropping-a-specific-extent"></a>特定のエクステントを削除する

テーブル名が指定されている場合は、[テーブル管理権限](../management/access-control/role-based-authorization.md)が必要です。

テーブル名が指定されていない場合、[データベース管理者権限](../management/access-control/role-based-authorization.md)が必要です。

**構文**

`.drop``extent` *ExtentId* [ `from` *TableName*]

### <a name="dropping-specific-multiple-extents"></a>特定の複数のエクステントの削除

テーブル名が指定されている場合は、[テーブル管理権限](../management/access-control/role-based-authorization.md)が必要です。

テーブル名が指定されていない場合、[データベース管理者権限](../management/access-control/role-based-authorization.md)が必要です。

**構文**

`.drop``extents` `(` *ExtentId1* `,` ...*Extentidn* `)`[ `from` *TableName*]

### <a name="specifying-extents-by-properties"></a>プロパティによるエクステントの指定

テーブル名が指定されている場合は、[テーブル管理権限](../management/access-control/role-based-authorization.md)が必要です。

テーブル名が指定されていない場合、[データベース管理者権限](../management/access-control/role-based-authorization.md)が必要です。

`.drop``extents`[ `older` *N* ( `days`  |  `hours` )] `from` (*TableName*  |  `all` `tables` ) [ `trim` `by` ( `extentsize`  |  `datasize` ) *N* ( `MB`  |  `GB`  |  `bytes` )] [ `limit` *limitcount*]

* `older`: *N*日/時間よりも古いエクステントのみが削除されます。 
* `trim`: エクステントの合計が目的のサイズ (MaxSize) と一致するまで、データベース内のデータがトリミングされます。 
* `limit`: この操作は、最初の*Limitcount*エクステントに適用されます 

コマンドはエミュレーション (では `.drop-pretend` なく `.drop` ) モードをサポートします。これにより、コマンドが実行された場合と同様に出力が生成されますが、実際には実行されません。

**例**

10日前以上前に作成されたすべてのエクステントをデータベース内のすべてのテーブルから削除する`MyDatabase`

```kusto
.drop extents <| .show database MyDatabase extents | where CreatedOn < now() - time(10d)
```

テーブル内のすべてのエクステントを削除 `Table1` し `Table2` ます。作成時間が10日前を超えています。

```kusto
.drop extents older 10 days from tables (Table1, Table2)
```

エミュレーションモード: コマンドによってどのエクステントが削除されるかを示します (エクステント ID パラメーターは、このコマンドには適用できません)。

```kusto
.drop-pretend extents older 10 days from all tables
```

' TestTable ' からすべてのエクステントを削除します。

```kusto
.drop extents from TestTable
```
 
**出力を返す**

|出力パラメーター |種類 |説明 
|---|---|---
|ExtentId |String |コマンドの結果として削除された ExtentId 
|TableName |String |エクステントが属しているテーブル名  
|Event.manualintervention.createdon |DateTime |エクステントが最初に作成された日時に関する情報を保持するタイムスタンプ 
 
**出力例** 

|エクステント Id |テーブル名 |作成日時 
|---|---|---
|43c6e03f-1713-4ca7-a52a-5db8a4e8b87d |TestTable |2015-01-12 12:48: 49.4298178 

## <a name="replace-extents"></a>。エクステントの置換

**構文**

`.replace`[ `async` ] テーブル `extents` `in` `table` *DestinationTableName* `<| 
{` *query for extents to be dropped from table* `},{` *に移動するエクステント*のテーブルクエリから削除するエクステントの destinationtablename クエリ`}`

このコマンドは、特定のデータベースのコンテキストで実行され、指定したエクステントをソーステーブルからコピー先テーブルに移動し、指定したエクステントをコピー先テーブルから削除します。
すべての削除操作と移動操作は、1つのトランザクションで実行されます。

コピー元およびコピー先のテーブルに対する[Table admin 権限](../management/access-control/role-based-authorization.md)が必要です。

* `async`(省略可能) コマンドを非同期的に実行するかどうかを指定します (この場合、操作 ID (Guid) が返され[ます。](operations.md#show-operations)操作の状態は、[操作の表示] コマンドを使用して監視できます)。
    * このオプションが使用されている場合は、正常に実行された結果を取得でき[ます。 [操作の詳細の表示](operations.md#show-operation-details)] コマンド)。

削除または移動するエクステントを指定するには、2つのクエリを指定します。
- *テーブルから削除するエクステントのクエリ*-このクエリの結果では、エクステント id を指定します  
コピー先のテーブルから削除する必要があります。
- [*テーブルに移動するエクステントのクエリ*]-このクエリの結果では、変換先テーブルに移動するソーステーブルのエクステント id を指定します。

どちらのクエリも、"ExtentId" という名前の列を含むレコードセットを返す必要があります。

**制限事項**
- ソーステーブルとターゲットテーブルは両方ともコンテキストデータベースに存在する必要があります。 
- *テーブルから削除するエクステントのクエリ*で指定されたエクステントはすべて、対象のテーブルに属している必要があります。
- コピー元テーブルのすべての列は、同じ名前とデータ型の変換先テーブルに存在することが想定されています。

**出力を返す**(同期実行の場合)

出力パラメーター |種類 |説明 
---|---|---
OriginalExtentId |string |コピー先のテーブルに移動されたソーステーブル内の元のエクステントの一意識別子 (GUID)、または削除されたコピー先テーブルのエクステントの一意識別子 (GUID)。
ResultExtentId |string |コピー元のテーブルからコピー先のテーブルに移動された結果エクステントの一意識別子 (GUID)。または、対象テーブルからエクステントが削除された場合は-empty。 失敗時-"失敗"。
詳細 |string |操作が失敗した場合のエラーの詳細が含まれます。

> [!NOTE]
> *テーブルクエリから削除するエクステント*によって返されたエクステントが対象テーブルに存在しない場合、コマンドは失敗します。 これは、replace コマンドを実行する前にエクステントがマージされた場合に発生する可能性があります。 不足しているエクステントでコマンドが失敗するようにするには、クエリが予期される ExtentIds を返していることを確認します。 次の #1 例は、テーブルの MyOtherTable に削除するエクステントが存在しない場合に失敗します。 ただし #2 の例では、drop に対するクエリがエクステント id を返さなかったため、削除する範囲が存在しない場合でも成功します。 

**例**

次のコマンドは、2つの特定のテーブル (、) のすべてのエクステントを `MyTable1` `MyTable2` テーブルに移動し、でタグ付けされ `MyOtherTable` たすべてのエクステントを削除し `MyOtherTable` `drop-by:MyTag` ます。

```kusto
.replace extents in table MyOtherTable <|
    {
        .show table MyOtherTable extents where tags has 'drop-by:MyTag'
    },
    {
        .show tables (MyTable1,MyTable2) extents
    }
```

**出力例** 

|OriginalExtentId |ResultExtentId |詳細
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42 187872f| 
|2dfdef64-62a3-4950-a13096b5b1083b5a |0fbの 3da-5e2847 f09-a000-e62eb41592df| 


次のコマンドは、1つの特定のテーブル () のすべてのエクステントを `MyTable1` テーブルに移動 `MyOtherTable` し、 `MyOtherTable` その ID によって内の特定のエクステントを削除します。


```kusto
.replace extents in table MyOtherTable <|
    {
        print ExtentId = "2cca5844-8f0d-454e-bdad-299e978be5df"
    },
    {
        .show table MyTable1 extents 
    }
```

```kusto
.replace extents in table MyOtherTable  <|
    {
        .show table MyOtherTable extents
        | where ExtentId == guid(2cca5844-8f0d-454e-bdad-299e978be5df) 
    },
    {
        .show table MyTable1 extents 
    }
```

次のコマンドは、べき等ロジックを実装します。これにより、テーブルから `t_dest` テーブルに移動するエクステントがある場合にのみ、テーブルからエクステントが削除され `t_source` `t_dest` ます。

```kusto
.replace async extents in table t_dest <|
{
    let any_extents_to_move = toscalar( 
        t_source
        | where extent_tags() has 'drop-by:blue'
        | summarize count() > 0
    );
    let extents_to_drop =
        t_dest
        | where any_extents_to_move and extent_tags() has 'drop-by:blue'
        | summarize by ExtentId = extent_id()
    ;
    extents_to_drop
},
{
    let extents_to_move = 
        t_source
        | where extent_tags() has 'drop-by:blue'
        | summarize by ExtentId = extent_id()
    ;
    extents_to_move
}
```

## <a name="drop-extent-tags"></a>。エクステントタグを削除します。

**構文**

`.drop`[ `async` ] `extent` `tags` `from` `table` *TableName* `(` '*Tag1*' [ `,` '*Tag2*' `,` ... `,` '*Tagn*']`)`

`.drop`[ `async` ] `extent` `tags`  <|  *クエリ*

* `async`(省略可能) コマンドを非同期的に実行するかどうかを指定します (この場合、操作 ID (Guid) が返され[ます。](operations.md#show-operations)操作の状態は、[操作の表示] コマンドを使用して監視できます)。
    * このオプションが使用されている場合は、正常に実行された結果を取得でき[ます。 [操作の詳細の表示](operations.md#show-operation-details)] コマンド)。

このコマンドは、特定のデータベースのコンテキストで実行され、指定された[エクステントタグ](extents-overview.md#extent-tagging)を、提供されたデータベースおよびテーブル内の任意の範囲から削除します。これには、任意のタグが含まれます。  

どの範囲から削除する必要があるかを指定するには、次の2つの方法があります。

1. 指定されたテーブル内のすべてのエクステントから削除する必要があるタグを明示的に指定する。
2. 結果を持つクエリを指定することにより、テーブル内のエクステント Id と foreach エクステント-削除する必要があるタグを指定します。

**制限事項**
- すべてのエクステントはコンテキストデータベースに存在する必要があり、同じテーブルに属している必要があります。 

**クエリを使用したエクステントの指定**

関連するすべてのソーステーブルとターゲットテーブルに対する[Table admin 権限](../management/access-control/role-based-authorization.md)が必要です。

```kusto 
.drop extent tags <| ...query... 
```

エクステントと削除するタグは、Kusto クエリを使用して指定します。このクエリでは、"ExtentId" という名前の列と "Tags" という列を含むレコードセットが返されます。 

*注*: [kusto .net クライアントライブラリ](../api/netfx/about-kusto-data.md)を使用する場合、必要なコマンドを生成するために次のメソッドを使用できます。
- `CslCommandGenerator.GenerateExtentTagsDropByRegexCommand(string tableName, string regex)`
- `CslCommandGenerator.GenerateExtentTagsDropBySubstringCommand(string tableName, string substring)`

**出力を返す**

出力パラメーター |種類 |説明 
---|---|---
OriginalExtentId |string |タグが変更された (操作の一部として削除される) 元のエクステントの一意識別子 (GUID) 
ResultExtentId |string |変更されたタグを持つ結果エクステントの一意の識別子 (GUID) (と、操作の一部として作成され、追加されます)。 失敗時-"失敗"。
ResultExtentTags |string |結果エクステントにタグが付けられているタグのコレクション (残っている場合)、または操作が失敗した場合は "null"。
詳細 |string |操作が失敗した場合のエラーの詳細が含まれます。

**例**

`drop-by:Partition000`タグが付けられているテーブル内の任意の範囲からタグを削除 `MyOtherTable` します。

```kusto
.drop extent tags from table MyOtherTable ('drop-by:Partition000')
```

タグ `drop-by:20160810104500` `a random tag` `drop-by:20160810` が付けられているテーブル内の任意の範囲から、、、またはのいずれかのタグを削除し `My Table` ます。

```kusto
.drop extent tags from table [My Table] ('drop-by:20160810104500','a random tag','drop-by:20160810')
```

`drop-by`テーブル内のエクステントからすべてのタグを削除 `MyTable` します。

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n') 
  | mv-expand Tags to typeof(string)
  | where Tags startswith 'drop-by'
```

`drop-by:StreamCreationTime_20160915(\d{6})`テーブル内のエクステントから regex に一致するすべてのタグを削除 `MyTable` します。

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n')
  | mv-expand Tags to typeof(string)
  | where Tags matches regex @"drop-by:StreamCreationTime_20160915(\d{6})"
```

**出力例** 

|OriginalExtentId |ResultExtentId | ResultExtentTags | 詳細
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | Partition001 |
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | |
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42 187872f | Partition001 Partition002 |
|2dfdef64-62a3-4950-a13096b5b1083b5a |0fbの 3da-5e2847 f09-a000-e62eb41592df | |

## <a name="alter-extent-tags"></a>. alter extent タグ

**構文**

`.alter`[ `async` ] `extent` `tags` `(` '*Tag1*' [ `,` '*Tag2*' `,` ... `,` '*Tagn*'] `)`  <|  *クエリ*

このコマンドは、特定のデータベースのコンテキストで実行され、指定されたクエリによって返されるすべてのエクステントの[エクステントタグ](extents-overview.md#extent-tagging)を、指定されたタグのセットに変更します。

エクステントと変更するタグは、"ExtentId" という名前の列を含むレコードセットを返す Kusto クエリを使用して指定します。

* `async`(省略可能) コマンドを非同期的に実行するかどうかを指定します (この場合、操作 ID (Guid) が返され[ます。](operations.md#show-operations)操作の状態は、[操作の表示] コマンドを使用して監視できます)。
    * このオプションが使用されている場合は、正常に実行された結果を取得でき[ます。 [操作の詳細の表示](operations.md#show-operation-details)] コマンド)。

すべての関連テーブルに対して[Table admin 権限](../management/access-control/role-based-authorization.md)が必要です。

**制限事項**
- すべてのエクステントはコンテキストデータベースに存在する必要があり、同じテーブルに属している必要があります。 

**出力を返す**

出力パラメーター |種類 |説明 
---|---|---
OriginalExtentId |string |タグが変更された (操作の一部として削除される) 元のエクステントの一意識別子 (GUID) 
ResultExtentId |string |変更されたタグを持つ結果エクステントの一意の識別子 (GUID) (と、操作の一部として作成され、追加されます)。 失敗時-"失敗"。
ResultExtentTags |string |結果エクステントがタグ付けされているタグのコレクション。操作が失敗した場合は "null"。
詳細 |string |操作が失敗した場合のエラーの詳細が含まれます。

**例**

テーブル内のすべてのエクステントのタグ `MyTable` をに変更 `MyTag` します。

```kusto
.alter extent tags ('MyTag') <| .show table MyTable extents
```

でタグ付けされたテーブル内のすべてのエクステントのタグ `MyTable` を `drop-by:MyTag` およびに変更し `drop-by:MyNewTag` `MyOtherNewTag` ます。

```kusto
.alter extent tags ('drop-by:MyNewTag','MyOtherNewTag') <| .show table MyTable extents where tags has 'drop-by:MyTag'
```

**出力例** 

|OriginalExtentId |ResultExtentId | ResultExtentTags | 詳細
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | ドロップダウン: MyNewTag MyOtherNewTag| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | ドロップダウン: MyNewTag MyOtherNewTag| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42 187872f | ドロップダウン: MyNewTag MyOtherNewTag| 
|2dfdef64-62a3-4950-a13096b5b1083b5a |0fbの 3da-5e2847 f09-a000-e62eb41592df | ドロップダウン: MyNewTag MyOtherNewTag| 

