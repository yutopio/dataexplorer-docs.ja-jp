---
title: ポリシーの更新 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの更新ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 95952a6f4e7a8c0d1a5b4207742e15873eb44c91
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519535"
---
# <a name="update-policy"></a>Update ポリシー

[更新ポリシー](updatepolicy.md)は、クエリを自動的に実行し、データが別のテーブルに取り込まれたときに結果を取得するテーブルレベルのポリシー オブジェクトです。

## <a name="show-update-policy"></a>更新ポリシーの表示

このコマンドは、指定されたテーブルの更新ポリシーを返すか、テーブル名として使用されている`*`場合はデフォルト データベース内のすべてのテーブルを返します。

**構文**

* `.show``table`*テーブル名*`policy``update`
* `.show``table`*データベース名*`.`*テーブル名*`policy``update`
* `.show` `table` `*` `policy` `update`

**戻り値**

このコマンドは、テーブルごとに 1 つのレコードを持つテーブルを返します。

|列    |Type    |説明                                                                                                                                                           |
|----------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EntityName|`string`|更新ポリシーが定義されているエンティティの名前                                                                                                                |
|ポリシー  |`string`|エンティティに定義されているすべての更新ポリシーを示す JSON 配列。 [update policy object](updatepolicy.md#the-update-policy-object)|

**例**

```kusto
.show table DerivedTableX policy update 
```

|EntityName        |ポリシー                                                                                                                                    |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
|[テストデータベース]。[派生テーブル]|[{"IsEnabled": true, "ソース": "MyTableX",クエリ": "MyUpdateFunction()"、IsTransactional": 偽、伝達インジングプロパティ": false}]|

## <a name="alter-update-policy"></a>更新ポリシーの変更

このコマンドは、指定されたテーブルの更新ポリシーを設定します。

**構文**

* `.alter``table`*TableName**ArrayOfUpdatePolicyObjects*オブジェクト`policy`名前`update`
* `.alter``table``update`*DatabaseName**TableName*`policy`*ArrayOfUpdatePolicyObjects*オブジェクト名テーブル名の配列の更新ポリシーオブジェクト`.`

*ゼロ*個以上の更新ポリシー オブジェクトが定義されている JSON 配列です。

**メモ**

1. 更新ポリシー オブジェクトの`Query`プロパティにストアドファンクションを使用することをお勧めします。
   これにより、ポリシー オブジェクト全体ではなく、関数定義だけを簡単に変更できます。

2. 更新ポリシーが設定されている場合は、次の検証が更新ポリシーに対して`IsEnabled``true`実行されます (設定されている場合)。
    1. `Source`: テーブルはターゲット データベースに存在する必要があります。
    2. `Query`: 
        * スキーマによって定義されるスキーマは、ターゲットテーブルの 1 つと一致する必要があります。 
        * クエリは、更新ポリシー`source`のテーブルを参照する必要があります。 source を参照*しない*更新ポリシー クエリを定義するには、with プロパティ`AllowUnreferencedSourceTable=true`(以下の例を参照) で設定できますが、パフォーマンス上の理由から一般的にはお勧めできません (ソース テーブルへのインジェストごとに、更新ポリシーの実行に対して異なるテーブル*内のすべての*レコードが考慮されることを意味します)。
    3. ポリシーは、ターゲット データベースの更新ポリシーのチェーンで作成されたサイクルを結果としては発生しません。
    4. に設定`IsTransactional``true`されている場合、`TableAdmin`権限は (ソース`Source`テーブル) に対しても検証されます。
  
3. 更新ポリシー /関数のパフォーマンスをテストしてから、ソース テーブルへの各インジェストで実行するように適用[してください。](updatepolicy.md#testing-an-update-policys-performance-impact)

**戻り値**

このコマンドは、テーブルの更新ポリシー オブジェクトを設定し (定義されている現在のポリシーがあればオーバーライドします)、対応する[.show table TABLE update policy](#show-update-policy)コマンドの出力を返します。

**例**

```kusto
// Creating function that will be used for update
.create function 
MyUpdateFunction()
{
    MyTableX
    | where ColumnA == 'some-string'
    | summarize MyCount=count() by ColumnB, Key=ColumnC
    | join (OtherTable | project OtherColumnZ, Key=OtherColumnC) on Key
    | project ColumnB, ColumnZ=OtherColumnZ, Key, MyCount
}

// Creating the target table (in case it doesn't already exist)
.set-or-append DerivedTableX <| MyUpdateFunction() | limit 0

// Use update policy on table DerivedTableX
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyUpdateFunction()", "IsTransactional": false, "PropagateIngestionProperties": false}]'
```

- ソース表 (この場合`MyTableX`) への取り込みが発生すると、その表に 1 つ以上のエクステント (データ・シャード) が作成されます。
- 更新`Query`ポリシー オブジェクトで定義されているのは、`MyUpdateFunction()`そのエクステントでのみ実行され、テーブル全体では実行されません。
  - この 「スコープ」は内部的かつ自動的に行われ、`Query`定義するときには処理しないでください。
  - 派生テーブルへの取り込み時に、新たに取り込まれたレコード (各取り込み操作で異なる) のみが考慮`DerivedTableX`されます (この場合)。


```kusto
// The following example will throw an error for not referencing the source table in the update policy query
// The policy's source table is MyTableX, whereas the query only references MyOtherTable. 
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

// Adding the following properties will suppress the error (but is not recommended)
.alter table DerivedTableX policy update with (AllowUnreferencedSourceTable=true)
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

```

## <a name="alter-merge-table-table-policy-update"></a>変更マージ テーブルの TABLE ポリシーの更新

このコマンドは、指定されたテーブルの更新ポリシーを変更します。

**構文**

* `.alter-merge``table`*TableName**ArrayOfUpdatePolicyObjects*オブジェクト`policy`名前`update`
* `.alter-merge``table``update`*DatabaseName**TableName*`policy`*ArrayOfUpdatePolicyObjects*オブジェクト名テーブル名の配列の更新ポリシーオブジェクト`.`

*ゼロ*個以上の更新ポリシー オブジェクトが定義されている JSON 配列です。

**メモ**

1. 更新ポリシー オブジェクトのクエリ プロパティの一括実装には、ストアド関数を使用することをお勧めします。 これにより、ポリシー オブジェクト全体ではなく、関数定義だけを簡単に変更できます。

2. `alter`コマンドに対して実行されるコマンドの場合、更新ポリシーで実行されるのと同`alter-merge`じ検証が実行されます。

**戻り値**

このコマンドは、テーブルの更新ポリシー オブジェクトに追加され (定義されている現在のポリシーがあればオーバーライドされます)、対応する[.show table TABLE 更新ポリシー](#show-update-policy)コマンドの出力を返します。

**例**

```kusto
.alter-merge table DerivedTableX policy update 
@'[{"IsEnabled": true, "Source": "MyTableY", "Query": "MyUpdateFunction()", "IsTransactional": false}]'  
``` 

## <a name="delete-table-table-policy-update"></a>テーブルの削除 テーブル ポリシーの更新

指定したテーブルの更新ポリシーを削除します。

**構文**

* `.delete``table`*テーブル名*`policy``update`
* `.delete``table`*データベース名*`.`*テーブル名*`policy``update`

**戻り値**

このコマンドは、テーブルの更新ポリシー オブジェクトを削除し、対応する[.show table TABLE 更新ポリシー](#show-update-policy)コマンドの出力を返します。

**例**

```kusto
.delete table DerivedTableX policy update 
```