---
title: 。エクステントタグを削除する-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの [エクステントタグの削除] コマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 23784a1e3e00c242665a43dffcc528bfeff68896
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060695"
---
# <a name="drop-extent-tags"></a>。エクステントタグを削除します。

コマンドは、特定のデータベースのコンテキストで実行されます。 データベースとテーブルのすべてまたは特定のエクステントから特定の[エクステントタグ](extents-overview.md#extent-tagging)が削除されます。  

> [!NOTE]
> データシャードは Kusto では**エクステント**と呼ばれ、すべてのコマンドはシノニムとして "エクステント" または "エクステント" を使用します。
> エクステントの詳細については、「[エクステント (データシャード) の概要](extents-overview.md)」を参照してください。

どの範囲から削除する必要があるかを指定するには、次の2つの方法があります。

* 指定されたテーブル内のすべてのエクステントから削除する必要があるタグを明示的に指定します。
* テーブル内のエクステント Id と、各エクステントの削除するタグを指定する結果を持つクエリを指定します。

## <a name="syntax"></a>構文

`.drop`[ `async` ] `extent` `tags` `from` `table` *TableName* `(` '*Tag1*' [ `,` '*Tag2*' `,` ... `,` '*Tagn*']`)`

`.drop`[ `async` ] `extent` `tags`  <|  *クエリ*

`async`(省略可能): コマンドを非同期に実行します。
   * 操作 ID (Guid) が返されます。
   * 操作の状態を監視できます。 [[操作の表示](operations.md#show-operations)] コマンドを使用します。
   * [[操作の詳細を表示](operations.md#show-operation-details)] コマンドを使用して、正常に実行された結果を取得します。

## <a name="restrictions"></a>制限

すべてのエクステントはコンテキストデータベースに存在する必要があり、同じテーブルに属している必要があります。

## <a name="specify-extents-with-a-query"></a>クエリでエクステントを指定する

エクステントと削除するタグは、Kusto クエリを使用して指定します。 このメソッドは、"ExtentId" という名前の列と "Tags" という列を含むレコードセットを返します。

> [!NOTE]
> [Kusto .net クライアントライブラリ](../api/netfx/about-kusto-data.md)を使用する場合、次のメソッドによって必要なコマンドが生成されます。
> * `CslCommandGenerator.GenerateExtentTagsDropByRegexCommand(string tableName, string regex)`
> * `CslCommandGenerator.GenerateExtentTagsDropBySubstringCommand(string tableName, string substring)`

関連するすべてのソーステーブルとターゲットテーブルに対する[Table admin 権限](../management/access-control/role-based-authorization.md)が必要です。

### <a name="syntax-for-drop-extent-tags-in-query"></a>クエリ内のエクステントタグを削除するための構文

```kusto 
.drop extent tags <| ...query...
```

### <a name="return-output"></a>出力を返す

出力パラメーター |Type |説明 
---|---|---
OriginalExtentId |string |タグが変更された元のエクステントの一意識別子 (GUID)。 エクステントは操作の一部として削除されます。
ResultExtentId |string |変更されたタグを持つ結果エクステントの一意識別子 (GUID)。 エクステントが作成され、操作の一部として追加されます。 失敗時-"失敗"。
ResultExtentTags |string |結果エクステントにタグが付けられているタグのコレクション (残っている場合)。操作が失敗した場合は "null"。
説明 |string |操作が失敗した場合のエラーの詳細が含まれます。

## <a name="examples"></a>例

### <a name="drop-one-tag"></a>1つのタグを削除します

`drop-by:Partition000`タグが付けられているテーブル内の任意のエクステントからタグを削除します。

```kusto
.drop extent tags from table MyOtherTable ('drop-by:Partition000')
```

### <a name="drop-several-tags"></a>複数のタグを削除する

タグ `drop-by:20160810104500` `a random tag` `drop-by:20160810` が付けられているテーブル内の任意の範囲からタグ、、を削除します。

```kusto
.drop extent tags from table [My Table] ('drop-by:20160810104500','a random tag','drop-by:20160810')
```

### <a name="drop-all-drop-by-tags"></a>すべての `drop-by` タグを削除 

`drop-by`テーブルのエクステントからすべてのタグを削除し `MyTable` ます。

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n') 
  | mv-expand Tags to typeof(string)
  | where Tags startswith 'drop-by'
```

### <a name="drop-all-tags-matching-specific-regex"></a>特定の正規表現に一致するすべてのタグを削除する 

`drop-by:StreamCreationTime_20160915(\d{6})`テーブルのエクステントから regex に一致するすべてのタグを削除し `MyTable` ます。

```kusto
.drop extent tags <| 
  .show table MyTable extents 
  | where isnotempty(Tags)
  | extend Tags = split(Tags, '\r\n')
  | mv-expand Tags to typeof(string)
  | where Tags matches regex @"drop-by:StreamCreationTime_20160915(\d{6})"
```

## <a name="sample-output"></a>サンプル出力

|OriginalExtentId |ResultExtentId | ResultExtentTags | 説明
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | Partition001 |
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | |
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42 187872f | Partition001 Partition002 |
|2dfdef64-62a3-4950-a13096b5b1083b5a |0fbの 3da-5e2847 f09-a000-e62eb41592df | |
