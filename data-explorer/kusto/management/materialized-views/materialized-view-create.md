---
title: 具体化ビューの作成-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーで具体化されたビューを作成する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 95f8ce19c6edb419de4fb5053a79c243e3e332c4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252823"
---
# <a name="create-materialized-view"></a>.create materialized-view

具体化された [ビュー](materialized-view-overview.md) は、1つの集計ステートメントを表す、ソーステーブルに対する集計クエリです。

具体化されたビューを作成するには、次の2つの方法があります。これは、コマンドの *バックフィル* オプションによって示されます。

 * **ソーステーブル内の既存のレコードに基づいて作成します。** 
      * ソーステーブル内のレコードの数によっては、作成に時間がかかることがあります。 ビューは、完了するまでクエリで使用できません。
      * このオプションを使用する場合、create コマンドはである必要があり、実行を監視するには `async` 、[ [操作の表示](../operations.md#show-operations) ] コマンドを使用します。

    * バックフィルプロセスを取り消すには、 [[操作の取り消し](#cancel-materialized-view-creation) ] コマンドを使用します。

      > [!IMPORTANT]
      > * コールドキャッシュのデータでは、バックフィルオプションの使用はサポートされていません。 必要に応じて、ビューの作成に使用するホットキャッシュ期間を増やします。 この場合、スケールアウトが必要になることがあります。    
      > * 大きなソーステーブルの場合、バックフィルオプションの使用には時間がかかることがあります。 このプロセスが実行中に失敗すると、自動的に再試行されず、create コマンドの再実行が必要になります。
    
* **ここから具体化されたビューを作成します。** 
    * 具体化されたビューは空で作成され、ビュー作成後のレコードの取り込まれたのみが含まれます。 この種類の作成は直ちに返され、不要になり、 `async` ビューはすぐにクエリに使用できるようになります。

Create 操作には、 [データベース管理者](../access-control/role-based-authorization.md) のアクセス許可が必要です。 具体化されたビューの作成者がその管理者になります。

## <a name="syntax"></a>構文

`.create` [`async`] `materialized-view` <br>
[ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ] <br>
*ViewName* `on table`*Sourcetablename* <br>
`{`<br>&nbsp;&nbsp;&nbsp;&nbsp;*照会*<br>`}`

## <a name="arguments"></a>引数

|引数|Type|説明
|----------------|-------|---|
|ViewName|String|具体化したビューの名前。 ビュー名が同じデータベース内のテーブル名または関数名と競合しており、 [識別子の名前付け規則](../../query/schema-entities/entity-names.md#identifier-naming-rules)に従っている必要があります。 |
|Targettablename|String|ビューが定義されているソーステーブルの名前。|
|クエリ|String|具体化されたビュークエリ。 詳細については、「 [クエリ](#query-argument)」を参照してください。|

### <a name="query-argument"></a>クエリ引数

具体化されたビュー引数で使用されるクエリは、次の規則によって制限されます。

* クエリ引数は、具体化されたビューのソースである1つのファクトテーブルを参照し、1つの集計演算子を含め、1つ以上の集計関数を式によって1つ以上のグループによって集計する必要があります。 集計演算子は、常にクエリの最後の演算子である必要があります。

* ビューは `arg_max` / `arg_min` / `any` ビュー (これらの関数は同じビューで一緒に使用できます) または他のサポートされている関数のいずれかになりますが、両方を同じ具体化されたビューで使用することはできません。 
    たとえば、`SourceTable | summarize arg_max(Timestamp, *), count() by Id` はサポートされていません。 

* クエリには、またはに依存する演算子を含めること `now()` は `ingestion_time()` できません。 たとえば、クエリにを含めることは `where Timestamp > ago(5d)` できません。 集計を含む具体化されたビューには、 `arg_max` / `arg_min` / `any` 他のサポートされている集計関数を含めることはできません。 具体化されたビューの保持ポリシーを使用して、ビューの対象となる期間を制限します。

* 複合集計は、具体化されたビュー定義ではサポートされていません。 たとえば、次のビューではなく、 `SourceTable | summarize Result=sum(Column1)/sum(Column2) by Id` 具体化されたビューをとして定義します `SourceTable | summarize a=sum(Column1), b=sum(Column2) by Id` 。 ビューのクエリ時に、を実行 `ViewName | project Id, Result=a/b` します。 計算列 () を含む、ビューの必要な出力は、 `a/b` 格納されている [関数](../../query/functions/user-defined-functions.md)にカプセル化できます。 具体化されたビューに直接アクセスするのではなく、格納されている関数にアクセスします。

* クラスター間または複数データベースにまたがるクエリはサポートされていません。

* [External_table ()](../../query/externaltablefunction.md)および[externaldata](../../query/externaldata-operator.md)への参照はサポートされていません。

* ビューのソーステーブルに加えて、1つまたは複数を参照することもでき [`dimension tables`](../../concepts/fact-and-dimension-tables.md) ます。 ディメンションテーブルは、ビューのプロパティで明示的に呼び出す必要があります。 ディメンションテーブルを結合するときの動作を理解することが重要です。

    * ビューのソーステーブル (ファクトテーブル) 内のレコードは、1回だけ具体化されます。 ファクトテーブルとディメンションテーブル間のインジェストの遅延が異なると、ビューの結果に影響を与える可能性があります。

    * **例**: ビュー定義には、ディメンションテーブルとの内部結合が含まれています。 具体化の時点では、ディメンションレコードは完全には取り込まれたませんでしたが、既にファクトテーブルに取り込まれたされていました。 このレコードはビューから削除され、再処理されることはありません。 

        同様に、結合が外部結合の場合、ファクトテーブルのレコードが処理され、ディメンションテーブルの列に null 値を持つビューに追加されます。 ビューに既に追加されている (null 値を持つ) レコードは、再度処理されません。 ディメンションテーブルの列の値は、null のままになります。

## <a name="properties"></a>Properties

句では、次のものがサポートされてい `with(propertyName=propertyValue)` ます。 すべてのプロパティは省略可能です。

|プロパティ|Type|説明 |
|----------------|-------|---|
|バック|[bool]|現在、 *SourceTable* () にあるすべてのレコードに基づいてビューを作成するか `true` 、"後から" () を作成するかを指定し `false` ます。 既定値は `false`です。| 
|effectiveDateTime|DATETIME| と共に指定した場合、作成されるのは、 `backfill=true` datetime の後の取り込まれたレコードだけです。 バックフィルも true に設定する必要があります。 Datetime リテラルが必要です。次に例を示します。 `effectiveDateTime=datetime(2019-05-01)`|
|dimensionTables|Array|ビュー内のディメンションテーブルのコンマ区切りの一覧です。 [クエリ引数](#query-argument)を参照してください
|autoUpdateSchema|[bool]|ソーステーブルの変更時にビューを自動更新するかどうかを指定します。 既定値は `false`です。 このオプションは、型のビュー `arg_max(Timestamp, *)`  /  `arg_min(Timestamp, *)`  /  `any(*)` (列の引数がの場合のみ `*` ) に対してのみ有効です。 このオプションが true に設定されている場合、ソーステーブルへの変更は具体化されたビューに自動的に反映されます。
|folder|string|具体化されたビューのフォルダー。|
|docString|string|具体化されたビューを文書化する文字列|

> [!WARNING]
> * を使用する `autoUpdateSchema` と、ソーステーブルの列が削除されたときにデータ損失を元に戻すことができなくなる可能性があります。 
> * ソーステーブルに変更が加えられ、具体化されたビューに対するスキーマ変更が発生し、 `autoUpdateSchema` が false の場合、ビューは自動的に無効になります。 
>    * このエラーは、を使用してソーステーブルに列を追加する場合によく見られ `arg_max(Timestamp, *)` ます。 
>    * このエラーを回避するには、ビュークエリをとして定義する `arg_max(Timestamp, Column1, Column2, ...)` か、オプションを使用し `autoUpdateSchema` ます。
> * これらの理由でビューが無効になっている場合は、[具体化された [ビューを有効に](materialized-view-enable-disable.md) する] コマンドを使用して問題を修正した後で、再度有効にすることができます。
>

## <a name="examples"></a>例

1. 現在から取り込まれたレコードのみを具体化する空の arg_max ビューを作成します。

    <!-- csl -->
    ```
    .create materialized-view ArgMax on table T
    {
        T | summarize arg_max(Timestamp, *) by User
    }
    ```
    
1. 次を使用して、バックフィルオプションを使用して日次集計の具体化されるビューを作成し `async` ます。

    <!-- csl -->
    ```
    .create async materialized-view with (backfill=true, docString="Customer telemetry") CustomerUsage on table T
    {
        T 
        | extend Day = bin(Timestamp, 1d)
        | summarize count(), dcount(User), max(Duration) by Customer, Day 
    } 
    ```
    
1. バックフィルとを使用して、具体化されたビューを作成し `effectiveDateTime` ます。 ビューは、datetime のレコードのみに基づいて作成されます。

    <!-- csl -->
    ```
    .create async materialized-view with (backfill=true, effectiveDateTime=datetime(2019-01-01)) CustomerUsage on table T 
    {
        T 
        | extend Day = bin(Timestamp, 1d)
        | summarize count(), dcount(User), max(Duration) by Customer, Day
    } 
    ```
1. EventId 列に基づいて、ソーステーブルを重複除去する具体化されたビュー。

    <!-- csl -->
    ```
    .create materialized-view DedupedT on table T
    {
        T
        | summarize any(*) by EventId
    }
    ```

1. が最後のものである限り、定義には、ステートメントの前に追加の演算子を含めることができ `summarize` `summarize` ます。

    <!-- csl -->
    ```
    .create materialized-view CustomerUsage on table T 
    {
        T 
        | where Customer in ("Customer1", "Customer2", "CustomerN")
        | parse Url with "https://contoso.com/" Api "/" *
        | extend Month = startofmonth(Timestamp)
        | summarize count(), dcount(User), max(Duration) by Customer, Api, Month
    }
    ```

1. ディメンションテーブルと結合する具体化されたビュー:

    <!-- csl -->
    ```
    .create materialized-view EnrichedArgMax on table T with (dimensionTables = ['DimUsers'])
    {
        T
        | lookup DimUsers on User  
        | summarize arg_max(Timestamp, *) by User 
    }
    
    .create materialized-view EnrichedArgMax on table T with (dimensionTables = ['DimUsers'])
    {
        DimUsers | project User, Age, Address
        | join kind=rightouter hint.strategy=broadcast T on User
        | summarize arg_max(Timestamp, *) by User 
    }
    ```
    

## <a name="supported-aggregation-functions"></a>サポートされている集計関数

次の集計関数がサポートされています。

* [`count`](../../query/count-aggfunction.md)
* [`countif`](../../query/countif-aggfunction.md)
* [`dcount`](../../query/dcount-aggfunction.md)
* [`dcountif`](../../query/dcountif-aggfunction.md)
* [`min`](../../query/min-aggfunction.md)
* [`max`](../../query/max-aggfunction.md)
* [`avg`](../../query/avg-aggfunction.md)
* [`avgif`](../../query/avgif-aggfunction.md)
* [`sum`](../../query/sum-aggfunction.md)
* [`arg_max`](../../query/arg-max-aggfunction.md)
* [`arg_min`](../../query/arg-min-aggfunction.md)
* [`any`](../../query/any-aggfunction.md)
* [`hll`](../../query/hll-aggfunction.md)
* [`make_set`](../../query/makeset-aggfunction.md)
* [`make_list`](../../query/makelist-aggfunction.md)
* [`percentile`, `percentiles`](../../query/percentiles-aggfunction.md)

## <a name="performance-tips"></a>パフォーマンスに関するヒント

* 具体化されたビュークエリフィルターは、具体化されたビューディメンションの1つ (集計 by 句) によってフィルター処理されるときに最適化されます。 クエリパターンが、多くの場合、具体化されたビューのディメンションである列によってフィルター処理されることがわかっている場合は、ビューに含めます。 たとえば、によってフィルター処理される、によってを公開する具体化されたビューの場合 `arg_max` `ResourceId` `SubscriptionId` 、次のような推奨事項があります。

    **操作**:
    
    ```kusto
    .create materialized-view ArgMaxResourceId on table FactResources
    {
        FactResources | summarize arg_max(Timestamp, *) by SubscriptionId, ResouceId 
    }
    ``` 
    
    **避け**てください。
    
    ```kusto
    .create materialized-view ArgMaxResourceId on table FactResources
    {
        FactResources | summarize arg_max(Timestamp, *) by ResouceId 
    }
    ```

* 具体化されたビュー定義の一部として [更新ポリシー](../updatepolicy.md) に移動できる、変換、正規化、およびその他の大量の計算は含めないでください。 代わりに、更新ポリシーでこれらのプロセスをすべて実行し、具体化されたビューでのみ集計を実行します。 このプロセスは、ディメンションテーブルの参照に使用します (該当する場合)。

    **操作**:
    
    * ポリシーの更新:
    
    ```kusto
    .alter-merge table Target policy update 
    @'[{"IsEnabled": true, 
        "Source": "SourceTable", 
        "Query": 
            "SourceTable 
            | extend ResourceId = strcat('subscriptions/', toupper(SubscriptionId), '/', resourceId)", 
        "IsTransactional": false}]'  
    ```
        
    * 具体化ビュー:
    
    ```kusto
    .create materialized-view Usage on table Events
    {
    &nbsp;     Target 
    &nbsp;     | summarize count() by ResourceId 
    }
    ```
    
    **避け**てください。
    
    ```kusto
    .create materialized-view Usage on table SourceTable
    {
    &nbsp;     SourceTable 
    &nbsp;     | extend ResourceId = strcat('subscriptions/', toupper(SubscriptionId), '/', resourceId)
    &nbsp;     | summarize count() by ResourceId
    }
    ```

> [!NOTE]
> クエリのパフォーマンスを最適化する必要があるが、データ鮮度を犠牲にする可能性がある場合は、 [materialized_view () 関数](../../query/materialized-view-function.md)を使用します。

## <a name="limitations-on-creating-materialized-views"></a>具体化されるビューの作成に関する制限事項

* 具体化されたビューを作成することはできません:
    * 別の具体化されたビューの上にあります。
    * [フォロワーデータベース](../../../follower.md)での。 フォロワーデータベースは読み取り専用で、具体化されたビューには書き込み操作が必要です。  リーダーデータベースで定義されている具体化されたビューは、リーダー内の他のテーブルと同様に、フォロワーからクエリを実行できます。 
* 具体化されたビューのソーステーブル:
    * [インジェストメソッド](../../../ingest-data-overview.md#ingestion-methods-and-tools)のいずれかを使用するか、[更新ポリシー](../updatepolicy.md)を使用するか、[クエリコマンドから](../data-ingestion/ingest-from-query.md)取り込むことによって、直接取り込まれたするテーブルを指定する必要があります。
        * 具体的には、他のテーブルの [移動エクステント](../move-extents.md) を具体化されたビューのソーステーブルに使用することはできません。 エクステントの移動は、次のエラーで失敗する場合があります `Cannot drop/move extents from/to table 'TableName' since Materialized View 'ViewName' is currently processing some of these extents` 。 
    * [IngestionTime ポリシー](../ingestiontimepolicy.md)が有効になっている必要があります (既定では有効になっています)。
    * ストリーミングインジェストを有効にすることはできません。
    * 制限付きのテーブル、または行レベルのセキュリティが有効になっているテーブルを指定することはできません。
* 具体化されたビューの上で[カーソル関数](../databasecursor.md#cursor-functions)を使用することはできません。
* 具体化されたビューからの連続エクスポートはサポートされていません。

## <a name="cancel-materialized-view-creation"></a>具体化したビューの作成を取り消す

オプションを使用する場合は、具体化されたビューの作成プロセスをキャンセルし `backfill` ます。 この操作は、作成に時間がかかり過ぎ、実行中に中止する場合に便利です。  

> [!WARNING]
> 具体化されたビューは、このコマンドの実行後に復元することはできません。

作成プロセスをすぐに中止することはできません。 Cancel コマンドは、具体化を停止するように通知し、キャンセルが要求されたかどうかを定期的に確認します。 Cancel コマンドは、具体化されたビューの作成プロセスが取り消されるまで最大10分間待機し、キャンセルが成功した場合はレポートを返します。 キャンセルが10分以内に成功しなかった場合でも、cancel コマンドによってエラーが報告された場合でも、具体化されたビューは、作成プロセスの後半で中止される可能性があります。 [操作の [表示](../operations.md#show-operations) ] コマンドは、操作が取り消されたかどうかを示します。 コマンドは、具体化された `cancel operation` ビューの作成の取り消しでのみサポートされ、他の操作をキャンセルするためにはサポートされていません。

### <a name="syntax"></a>構文

`.cancel` `operation` *operationId*

### <a name="properties"></a>Properties

|プロパティ|Type|説明
|----------------|-------|---|
|operationId|Guid|具体化されたビューの作成コマンドから返された操作 ID。|

### <a name="output"></a>出力

|出力パラメーター |Type |説明
|---|---|---
|OperationId|Guid|具体化されたビューの作成コマンドの操作 ID。
|操作|String|操作の種類。
|StartedOn|DATETIME|作成操作の開始時刻。
|CancellationState|string|- `Cancelled successfully` (作成がキャンセルされました)、 `Cancellation failed` (キャンセルがタイムアウトするまで待機)、( `Unknown` ビューの作成は現在実行されていませんが、この操作によって取り消されていません)。
|ReasonPhrase|string|キャンセルが成功しなかった理由。

### <a name="example"></a>例

<!-- csl -->
```
.cancel operation c4b29441-4873-4e36-8310-c631c35c916e
```

|OperationId|操作|StartedOn|CancellationState|ReasonPhrase|
|---|---|---|---|---|
|c4b29441-4873-4e36-8310-c631c35c916e|MaterializedViewCreateOrAlter|2020-05-08 19:45: 03.9184142|正常にキャンセルされました||

キャンセルが10分以内に完了しなかった場合、 `CancellationState` は失敗を示します。 作成は中止される可能性があります。
