---
title: . alter extent tags-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの alter extent コマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 00c4cfbb4b6415afcd68e8e41864ca4a68cc097e
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060679"
---
# <a name="alter-extent-tags"></a>. alter extent タグ

コマンドは、特定のデータベースのコンテキストで実行されます。 クエリによって返されたすべてのエクステントの指定された[エクステントタグ](extents-overview.md#extent-tagging)を変更します。

エクステントと変更するタグは、"ExtentId" という名前の列を含むレコードセットを返す Kusto クエリを使用して指定します。

すべての関連テーブルに対して[Table admin 権限](../management/access-control/role-based-authorization.md)が必要です。

> [!NOTE]
> データシャードは Kusto では**エクステント**と呼ばれ、すべてのコマンドはシノニムとして "エクステント" または "エクステント" を使用します。
> エクステントの詳細については、「[エクステント (データシャード) の概要](extents-overview.md)」を参照してください。

## <a name="syntax"></a>構文

`.alter`[ `async` ] `extent` `tags` `(` '*Tag1*' [ `,` '*Tag2*' `,` ... `,` '*Tagn*'] `)`  <|  *クエリ*

`async`(省略可能): コマンドを非同期に実行します。
   * 操作 ID (Guid) が返されます。 
   * 操作の状態を監視できます。 [[操作の表示](operations.md#show-operations)] コマンドを使用します。
   * 正常に実行された結果を取得できます。 [[操作の詳細を表示](operations.md#show-operation-details)] コマンドを使用します。

## <a name="restrictions"></a>制限

すべてのエクステントはコンテキストデータベースに存在する必要があり、同じテーブルに属している必要があります。

## <a name="return-output"></a>出力を返す

|出力パラメーター |Type |説明|
|---|---|---|
|OriginalExtentId |string |タグが変更された元のエクステントの一意識別子 (GUID)。 エクステントは操作の一部として削除されます。|
|ResultExtentId |string |変更されたタグを持つ結果エクステントの一意識別子 (GUID)。 エクステントが作成され、操作の一部として追加されます。 失敗時-"失敗"。|
|ResultExtentTags |string |結果エクステントにタグが付けられたタグのコレクション。操作が失敗した場合は "null"。|
|説明 |string |操作が失敗した場合のエラーの詳細が含まれます。|

## <a name="examples"></a>例

### <a name="alter-tags"></a>タグの変更 

テーブル内のすべてのエクステントのタグ `MyTable` をに変更します。`MyTag`

```kusto
.alter extent tags ('MyTag') <| .show table MyTable extents
```

### <a name="alter-tags-of-all-extents"></a>すべてのエクステントのタグを変更する

テーブル内のすべてのエクステントのタグを変更 `MyTable` します。にタグが付けられます。 `drop-by:MyTag` `drop-by:MyNewTag``MyOtherNewTag`

```kusto
.alter extent tags ('drop-by:MyNewTag','MyOtherNewTag') <| .show table MyTable extents where tags has 'drop-by:MyTag'
```

## <a name="sample-output"></a>サンプル出力

|OriginalExtentId |ResultExtentId | ResultExtentTags | 説明
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | ドロップダウン: MyNewTag MyOtherNewTag| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | ドロップダウン: MyNewTag MyOtherNewTag| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-b06b-08f42 187872f | ドロップダウン: MyNewTag MyOtherNewTag| 
|2dfdef64-62a3-4950-a13096b5b1083b5a |0fbの 3da-5e2847 f09-a000-e62eb41592df | ドロップダウン: MyNewTag MyOtherNewTag| 
