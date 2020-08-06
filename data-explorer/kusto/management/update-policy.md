---
title: Kusto update ポリシー管理-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの更新ポリシーについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/04/2020
ms.openlocfilehash: 111110ac69e726c8367af4a2741a79061df7531a
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803864"
---
# <a name="update-policy-commands"></a>ポリシーの更新コマンド

[更新ポリシー](updatepolicy.md)は、クエリを自動的に実行し、データが別のテーブルに取り込まれたされたときに結果を取り込みするテーブルレベルのポリシーオブジェクトです。

## <a name="show-update-policy"></a>更新ポリシーの表示

このコマンドは、指定されたテーブルの更新ポリシー、または `*` がテーブル名として使用されている場合は既定のデータベース内のすべてのテーブルを返します。

### <a name="syntax"></a>構文

* `.show``table` *TableName* TableName `policy``update`
* `.show``table` *DatabaseName* `.` *TableName* TableName `policy``update`
* `.show` `table` `*` `policy` `update`

### <a name="returns"></a>戻り値

このコマンドは、テーブルごとに1つのレコードを含むテーブルを返します。

|Column    |種類    |説明                                                                                                                                                           |
|----------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|EntityName|`string`|更新ポリシーが定義されているエンティティの名前                                                                                                                |
|ポリシー  |`string`|[更新ポリシーオブジェクト](updatepolicy.md#the-update-policy-object)として書式設定された、エンティティに対して定義されているすべての更新ポリシーを示す JSON 配列|

### <a name="example"></a>例

```kusto
.show table DerivedTableX policy update 
```

|EntityName        |ポリシー                                                                                                                                    |
|------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
|[TestDB]。[DerivedTableX]|[{"IsEnabled": true, "Source": "MyTableX", "Query": "Mytablex ()", "IsTransactional": false, "PropagateIngestionProperties": false}]|

## <a name="alter-update-policy"></a>更新ポリシーの変更

このコマンドは、指定されたテーブルの更新ポリシーを設定します。

### <a name="syntax"></a>構文

* `.alter``table` *TableName* `policy` TableName `update`*Arrayofupdatepolicyobjects*
* `.alter``table` *DatabaseName* `.` *TableName* `policy` `update` *arrayofupdatepolicyobjects*

*Arrayofupdatepolicyobjects*は、0個以上の更新ポリシーオブジェクトが定義されている JSON 配列です。

> [!NOTE]
> * 更新ポリシーオブジェクトのプロパティに格納されている関数を使用し `Query` ます。
   ポリシーオブジェクト全体ではなく、関数定義のみを変更する必要があります。
> * `IsEnabled`がに設定されている場合は `true` 、更新ポリシーが設定されているときに次の検証が実行されます。
>    * `Source`-対象データベースにテーブルが存在することを確認します。
>    * `Query` 
>        * スキーマで定義されているスキーマが対象テーブルのいずれかと一致することを確認します。
>        * クエリが更新ポリシーのテーブルを参照していることを確認し `source` ます。 
        ソースを参照しない更新ポリシークエリの定義は、のプロパティ (次の例を参照) でを設定することによって可能に `AllowUnreferencedSourceTable=true` なりますが、パフォーマンスの*with*問題により推奨されません。 ソーステーブルにインジェストを取り込むたびに、別のテーブル内のすべてのレコードが更新ポリシーの実行と見なされます。
 >       * ポリシーによって、ターゲットデータベースの更新ポリシーのチェーンに循環が作成されないことを確認します。
 > * がに設定されている場合 `IsTransactional` `true` 、は、 `TableAdmin` (ソーステーブル) に対してもアクセス許可が検証されることを確認し `Source` ます。
 > * 更新ポリシーまたは機能をテストして、ソーステーブルへのインジェストごとに実行するように適用します。 詳細については、「[更新ポリシーのパフォーマンスへの影響のテスト](updatepolicy.md#performance-impact)」を参照してください。

### <a name="returns"></a>戻り値

このコマンドは、現在のポリシーを上書きしてテーブルの更新ポリシーオブジェクトを設定し、対応する[テーブル更新ポリシーの表示](#show-update-policy)コマンドの出力を返します。

### <a name="example"></a>例

```kusto
// Create a function that will be used for update
.create function 
MyUpdateFunction()
{
    MyTableX
    | where ColumnA == 'some-string'
    | summarize MyCount=count() by ColumnB, Key=ColumnC
    | join (OtherTable | project OtherColumnZ, Key=OtherColumnC) on Key
    | project ColumnB, ColumnZ=OtherColumnZ, Key, MyCount
}

// Create the target table (if it doesn't already exist)
.set-or-append DerivedTableX <| MyUpdateFunction() | limit 0

// Use update policy on table DerivedTableX
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyUpdateFunction()", "IsTransactional": false, "PropagateIngestionProperties": false}]'
```

* この場合、ソーステーブルへのインジェストが発生すると、 `MyTableX` そのテーブルに1つ以上のエクステント (data シャード) が作成されます。
* `Query`更新ポリシーオブジェクトで定義されているは、この場合は `MyUpdateFunction()` これらのエクステントでのみ実行され、テーブル全体では実行されません。
  * "スコープ" は、内部的には自動的に実行され、を定義するときには処理しないで `Query` ください。
  * 派生テーブルに取り込みするときに、インジェスト操作ごとに異なる、新しく取り込まれたレコードのみが考慮され `DerivedTableX` ます。

```kusto
// The following example will throw an error for not referencing the source table in the update policy query
// The policy's source table is MyTableX, whereas the query only references MyOtherTable. 
.alter table DerivedTableX policy update
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

// Adding the following properties will suppress the error (but is not recommended)
.alter table DerivedTableX policy update with (AllowUnreferencedSourceTable=true)
@'[{"IsEnabled": true, "Source": "MyTableX", "Query": "MyOtherTable", "IsTransactional": false, "PropagateIngestionProperties": false}]'

```

## <a name="alter-merge-table-tablename-policy-update"></a>. alter-merge table *TableName*ポリシーの更新

このコマンドは、指定されたテーブルの更新ポリシーを変更します。

**構文**

* `.alter-merge``table` *TableName* `policy` TableName `update`*Arrayofupdatepolicyobjects*
* `.alter-merge``table` *DatabaseName* `.` *TableName* `policy` `update` *arrayofupdatepolicyobjects*

*Arrayofupdatepolicyobjects*は、0個以上の更新ポリシーオブジェクトが定義されている JSON 配列です。

> [!NOTE]
> * 更新ポリシーオブジェクトの query プロパティの一括実装には、格納されている関数を使用します。 
     ポリシーオブジェクト全体ではなく、関数の定義のみを変更する必要があります。
> * 検証は、コマンドで実行したものと同じ `alter` です。

**戻り値**

このコマンドは、現在のポリシーを上書きしてテーブルの更新ポリシーオブジェクトに追加し、対応するの出力を返し[ます。テーブル*TableName*更新ポリシーの表示](#show-update-policy)コマンドです。

**例**

```kusto
.alter-merge table DerivedTableX policy update 
@'[{"IsEnabled": true, "Source": "MyTableY", "Query": "MyUpdateFunction()", "IsTransactional": false}]'  
``` 

## <a name="delete-table-tablename-policy-update"></a>。テーブル*TableName*ポリシーの更新を削除します。

指定されたテーブルの更新ポリシーを削除します。

**構文**

* `.delete``table` *TableName* TableName `policy``update`
* `.delete``table` *DatabaseName* `.` *TableName* TableName `policy``update`

**戻り値**

このコマンドは、テーブルの更新ポリシーオブジェクトを削除し、対応するの出力を返し[ます。テーブル*TableName*更新ポリシー](#show-update-policy)コマンド。

**例**

```kusto
.delete table DerivedTableX policy update 
```
