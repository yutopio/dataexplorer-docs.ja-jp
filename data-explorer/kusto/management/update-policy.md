---
title: Kusto update ポリシー管理-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの更新ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 320824b4f614ea809141167c1284282b1ef641cf
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616560"
---
# <a name="update-policy"></a>Update ポリシー

[更新ポリシー](updatepolicy.md)は、データが別のテーブルに取り込まれたときにクエリを自動的に実行し、その結果を取り込むテーブルレベルのポリシーオブジェクトです。

## <a name="show-update-policy"></a>更新ポリシーの表示

このコマンドは、指定されたテーブルの更新ポリシー、またはがテーブル名と`*`して使用されている場合は既定のデータベース内のすべてのテーブルを返します。

**構文**

* `.show` `table` *TableName* `policy` `update`
* `.show``table` *DatabaseName*DatabaseName`.`*TableName* TableName `policy``update`
* `.show` `table` `*` `policy` `update`

**戻り値**

このコマンドは、テーブルごとに1つのレコードを含むテーブルを返します。次の列があります。

|列    |Type    |説明                                                                                                                                                           |
|----------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EntityName|`string`|更新ポリシーが定義されているエンティティの名前                                                                                                                |
|ポリシー  |`string`|[更新ポリシーオブジェクト](updatepolicy.md#the-update-policy-object)として書式設定された、エンティティに対して定義されているすべての更新ポリシーを示す JSON 配列|

**例**

```kusto
.show table DerivedTableX policy update 
```

|EntityName        |ポリシー                                                                                                                                    |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
|[TestDB]。[DerivedTableX]|[{"IsEnabled": true, "Source": "MyTableX", "Query": "Mytablex ()", "IsTransactional": false, "PropagateIngestionProperties": false}]|

## <a name="alter-update-policy"></a>更新ポリシーの変更

このコマンドは、指定されたテーブルの更新ポリシーを設定します。

**構文**

* `.alter``table` `update` *TableName* `policy` *arrayofupdatepolicyobjects*
* `.alter``table` *DatabaseName* `update` *ArrayOfUpdatePolicyObjects* *TableName* TableName `policy` `.`

*Arrayofupdatepolicyobjects*は、0個以上の更新ポリシーオブジェクトが定義されている JSON 配列です。

**メモ**

1. 1つは、更新ポリシーオブジェクトの`Query`プロパティに格納されている関数を使用することをお勧めします。
   これにより、ポリシーオブジェクト全体ではなく、関数定義だけを簡単に変更できるようになります。

2. 次の検証は、更新ポリシーが設定されているときに実行さ`IsEnabled`れます ( `true`がに設定されている場合)。
    1. `Source`: テーブルは、ターゲットデータベースに存在している必要があります。
    2. `Query`: 
        * スキーマで定義されているスキーマは、対象テーブルのいずれかと一致する必要があります。 
        * クエリでは、更新`source`ポリシーのテーブルを参照する必要があります。 ソースを参照し*ない*更新ポリシークエリの定義は、でプロパティを`AllowUnreferencedSourceTable=true`設定することによって可能になります (次の例を参照)。ただし、パフォーマンス上の理由から、通常は推奨されません (これは、ソーステーブルへのすべての取り込みで、更新ポリシーの実行では、別のテーブル内の*すべて*のレコードが考慮される
    3. ポリシーでは、ターゲットデータベースの更新ポリシーのチェーンに循環が作成されることはありません。
    4. がに`IsTransactional` `true`設定されて`TableAdmin`いる場合、アクセス`Source`許可は (ソーステーブル) に対しても検証されます。
  
3. ソーステーブルへのインジェストごとに実行するように更新ポリシー/関数を適用する前に、パフォーマンスをテストしてください。[こちら](updatepolicy.md#testing-an-update-policys-performance-impact)を参照してください。

**戻り値**

このコマンドは、テーブルの更新ポリシーオブジェクト (存在する場合は、定義されている現在のポリシーをオーバーライドします) を設定し、対応するテーブル[テーブル更新ポリシー](#show-update-policy)の出力を返します。

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

- ソーステーブルへのインジェスト (この場合`MyTableX`は) が発生すると、そのテーブルに1つ以上のエクステント (data シャード) が作成されます。
- 更新`Query`ポリシーオブジェクト (この場合`MyUpdateFunction()`は) で定義されているは、これらのエクステントでのみ実行され、テーブル全体では実行されません。
  - この "スコープ" は、 `Query`内部的には自動的に行われ、を定義するときには処理されません。
  - 派生テーブル (この場合`DerivedTableX`は) を取り込みするときに、新しく取り込まれたレコード (インジェスト操作ごとに異なる) だけが考慮されます。


```kusto
// The following example will throw an error for not referencing the source table in the update policy query
// The policy's source table is MyTableX, whereas the query only references MyOtherTable. 
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

// Adding the following properties will suppress the error (but is not recommended)
.alter table DerivedTableX policy update with (AllowUnreferencedSourceTable=true)
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

```

## <a name="alter-merge-table-table-policy-update"></a>. alter-マージテーブルのテーブルポリシーの更新

このコマンドは、指定されたテーブルの更新ポリシーを変更します。

**構文**

* `.alter-merge``table` `update` *TableName* `policy` *arrayofupdatepolicyobjects*
* `.alter-merge``table` *DatabaseName* `update` *ArrayOfUpdatePolicyObjects* *TableName* TableName `policy` `.`

*Arrayofupdatepolicyobjects*は、0個以上の更新ポリシーオブジェクトが定義されている JSON 配列です。

**メモ**

1. 更新ポリシーオブジェクトの query プロパティの一括実装には、ストアド関数を使用することをお勧めします。 これにより、ポリシーオブジェクト全体ではなく、関数定義だけを簡単に変更できるようになります。

2. コマンドがコマンドに対して実行される場合`alter` 、更新ポリシーに対して実行`alter-merge`される同じ検証がコマンドに対して実行されます。

**戻り値**

このコマンドは、テーブルの更新ポリシーオブジェクト (存在する場合は、定義されている現在のポリシーを上書きします) に追加し、対応するテーブル[テーブル更新ポリシー](#show-update-policy)コマンドの出力を返します。

**例**

```kusto
.alter-merge table DerivedTableX policy update 
@'[{"IsEnabled": true, "Source": "MyTableY", "Query": "MyUpdateFunction()", "IsTransactional": false}]'  
``` 

## <a name="delete-table-table-policy-update"></a>。テーブルテーブルポリシーの更新を削除します。

指定されたテーブルの更新ポリシーを削除します。

**構文**

* `.delete` `table` *TableName* `policy` `update`
* `.delete``table` *DatabaseName*DatabaseName`.`*TableName* TableName `policy``update`

**戻り値**

このコマンドは、テーブルの更新ポリシーオブジェクトを削除し、対応するテーブル[テーブル更新ポリシー](#show-update-policy)コマンドの出力を返します。

**例**

```kusto
.delete table DerivedTableX policy update 
```