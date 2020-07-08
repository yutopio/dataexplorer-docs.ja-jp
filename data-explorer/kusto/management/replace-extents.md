---
title: . エクステントの置換-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの [エクステントの置換] コマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: cd193cb370136fd7f14a8892f157a895a1d7ad50
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060774"
---
# <a name="replace-extents"></a>。エクステントの置換

このコマンドは、特定のデータベースのコンテキストで実行されます。
指定されたエクステントをソーステーブルから変換先テーブルに移動し、指定されたエクステントをコピー先テーブルから削除します。
すべての削除操作と移動操作は、1つのトランザクションで実行されます。

コピー元およびコピー先のテーブルに対する[Table admin 権限](../management/access-control/role-based-authorization.md)が必要です。

> [!NOTE]
> データシャードは Kusto では**エクステント**と呼ばれ、すべてのコマンドはシノニムとして "エクステント" または "エクステント" を使用します。
> エクステントの詳細については、「[エクステント (データシャード) の概要](extents-overview.md)」を参照してください。

## <a name="syntax"></a>構文

`.replace`[ `async` ] テーブル `extents` `in` `table` *DestinationTableName* `<| 
{` *query for extents to be dropped from table* `},{` *に移動するエクステント*のテーブルクエリから削除するエクステントの destinationtablename クエリ`}`

* `async`(省略可能): コマンドを非同期に実行します。
    * 操作 ID (Guid) が返されます。
    * 操作の状態を監視できます。 [[操作の表示](operations.md#show-operations)] コマンドを使用します。
    * 正常に実行された結果を取得できます。 [[操作の詳細を表示](operations.md#show-operation-details)] コマンドを使用します。

削除または移動するエクステントを指定するには、2つのクエリのいずれかを使用します。
* *テーブルから削除するエクステントのクエリ*: このクエリの結果では、対象テーブルから削除するエクステント id を指定します。
* [*テーブルに移動するエクステントのクエリ*: このクエリの結果では、変換先テーブルに移動するソーステーブルのエクステント id を指定します。

どちらのクエリも、"ExtentId" という名前の列を含むレコードセットを返す必要があります。

## <a name="restrictions"></a>制限

* ソーステーブルとターゲットテーブルは両方ともコンテキストデータベースに存在する必要があります。
* *テーブルから削除するエクステントのクエリ*で指定されたエクステントはすべて、対象のテーブルに属している必要があります。
* コピー元テーブルのすべての列は、同じ名前とデータ型の変換先テーブルに存在することが想定されています。

## <a name="return-output-for-sync-execution"></a>出力を返す (同期実行の場合)

出力パラメーター |Type |説明
---|---|---
OriginalExtentId |string |コピー先のテーブルに移動されたソーステーブル内の元のエクステントの一意識別子 (GUID)、または削除されたコピー先テーブルのエクステントの一意識別子 (GUID)。
ResultExtentId |string |ソーステーブルから変換先テーブルに移動された結果エクステントの一意識別子 (GUID)。 対象のテーブルからエクステントが削除された場合は空です。 失敗した場合: "Failed"。
説明 |string |操作が失敗した場合のエラーの詳細が含まれます。

> [!NOTE]
> *テーブルクエリから削除するエクステント*によって返されたエクステントが対象テーブルに存在しない場合、コマンドは失敗します。 これは、replace コマンドを実行する前にエクステントがマージされた場合に発生する可能性があります。
> 不足しているエクステントでコマンドが失敗するようにするには、クエリが予期される ExtentIds を返していることを確認します。 次の #1 例は、テーブルの*Myothertable*に削除するエクステントが存在しない場合に失敗します。 ただし #2 の例では、drop に対するクエリがエクステント Id を返さなかったため、削除する範囲が存在しない場合でも成功します。

## <a name="examples"></a>例

### <a name="move-all-extents-from-two-tables"></a>2つのテーブルからすべてのエクステントを移動する 

2つの特定のテーブル (、) のすべてのエクステントを `MyTable1` `MyTable2` テーブルに移動し、でタグ付けされ `MyOtherTable` たすべてのエクステントを削除し `MyOtherTable` `drop-by:MyTag` ます。

```kusto
.replace extents in table MyOtherTable <|
    {
        .show table MyOtherTable extents where tags has 'drop-by:MyTag'
    },
    {
        .show tables (MyTable1,MyTable2) extents
    }
```

#### <a name="sample-output"></a>サンプル出力

|OriginalExtentId |ResultExtentId |説明
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42 187872f| 
|2dfdef64-62a3-4950-a13096b5b1083b5a |0fbの 3da-5e2847 f09-a000-e62eb41592df| 

### <a name="move-all-extents-from-one-table-to-another-drop-specific-extent"></a>1つのテーブルから別のテーブルにエクステントをすべて移動し、特定のエクステントを削除する

特定の tavle () からテーブルにすべてのエクステントを移動 `MyTable1` `MyOtherTable` し、その ID で特定のエクステントを削除し `MyOtherTable` ます。

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

### <a name="implement-an-idempotent-logic"></a>べき等ロジックを実装する

べき等ロジックを実装します。これにより、テーブルから `t_dest` テーブルに移動するエクステントがある場合にのみ、Kusto はテーブルからエクステントを削除し `t_source` `t_dest` ます。

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
