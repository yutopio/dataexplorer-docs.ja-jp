---
title: 。エクステントの移動-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの [エクステントの移動] コマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 994f7077b1583317320cc561b2d5a5ae1cbe829f
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060711"
---
# <a name="move-extents"></a>。エクステントを移動します。

このコマンドは、特定のデータベースのコンテキストで実行されます。 指定されたエクステントをソーステーブルからコピー先テーブルに移動します。

このコマンドを実行するには、コピー元とコピー先のテーブルに対する[Table admin 権限](../management/access-control/role-based-authorization.md)が必要です。

> [!NOTE]
> データシャードは Kusto では**エクステント**と呼ばれ、すべてのコマンドはシノニムとして "エクステント" または "エクステント" を使用します。
> エクステントの詳細については、「[エクステント (データシャード) の概要](extents-overview.md)」を参照してください。

## <a name="syntax"></a>構文

`.move`[ `async` ] `extents` `all` `from` `table` *Sourcetablename* `to` `table` *destinationtablename*

`.move`[ `async` ] `extents` `(` *GUID1* [ `,` *GUID2* ...] `)` `from` `table`*Sourcetablename* `to``table` *Destinationtablename* 

`.move`[ `async` ] `extents` `to` `table` *Destinationtablename*  <|  *クエリ*

`async`(省略可能)。 コマンドを非同期に実行します。 
   * 操作 ID (Guid) が返されます。
   * 操作の状態を監視できます。 [[操作の表示](operations.md#show-operations)] コマンドを使用します。
   * 正常に実行された結果を取得できます。 [[操作の詳細を表示](operations.md#show-operation-details)] コマンドを使用します。

移動するエクステントを指定するには、次の3つの方法があります。
* 特定のテーブルのすべてのエクステントを移動します。
* ソーステーブルのエクステント Id を明示的に指定します。
* 結果がソーステーブルのエクステント Id を指定するクエリを指定します。

## <a name="restrictions"></a>制限

* ソーステーブルとターゲットテーブルは両方ともコンテキストデータベースに存在する必要があります。
* コピー元テーブルのすべての列は、同じ名前とデータ型の変換先テーブルに存在することが想定されています。

## <a name="specify-extents-with-a-query"></a>クエリでエクステントを指定する

```kusto
.move extents to table TableName <| ...query...
```

エクステントは、 *ExtentId*という列を含むレコードセットを返す Kusto クエリを使用して指定されます。

## <a name="return-output-for-sync-execution"></a>出力を返す (同期実行の場合)

出力パラメーター |Type |説明
---|---|---
OriginalExtentId |string |コピー先のテーブルに移動された、ソーステーブル内の元のエクステントの一意識別子 (GUID)。
ResultExtentId |string |ソーステーブルから変換先テーブルに移動された結果エクステントの一意識別子 (GUID)。 失敗時-"失敗"。
説明 |string |操作が失敗した場合のエラーの詳細が含まれます。

## <a name="examples"></a>例

### <a name="move-all-extents"></a>すべてのエクステントを移動する 

テーブル内のすべてのエクステント `MyTable` をテーブルに移動し `MyOtherTable` ます。

```kusto
.move extents all from table MyTable to table MyOtherTable
```

### <a name="move-two-specific-extents"></a>2つの特定のエクステントの移動 

2つの特定のエクステント (エクステント Id) をテーブルから `MyTable` テーブルに移動し `MyOtherTable` ます。

```kusto
.move extents (AE6CD250-BE62-4978-90F2-5CB7A10D16D7,399F9254-4751-49E3-8192-C1CA78020706) from table MyTable to table MyOtherTable
```

### <a name="move-all-extents-from-specific-tables"></a>特定のテーブルからすべてのエクステントを移動する 

特定の tavles からテーブルにすべてのエクステントを移動 `MyTable1` `MyTable2` し `MyOtherTable` ます。

```kusto
.move extents to table MyOtherTable <| .show tables (MyTable1,MyTable2) extents
```

## <a name="sample-output"></a>サンプル出力

|OriginalExtentId |ResultExtentId| 説明
|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42 187872f| 
|2dfdef64-62a3-4950-a13096b5b1083b5a |0fbの 3da-5e2847 f09-a000-e62eb41592df| 
