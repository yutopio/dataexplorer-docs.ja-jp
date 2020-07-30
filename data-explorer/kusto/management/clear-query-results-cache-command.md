---
title: クエリ結果のキャッシュのクリア-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでキャッシュされたデータベーススキーマをクリアするための管理コマンドについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 27806155d105a109c7419d04d945fbc854c44286
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349926"
---
# <a name="clear-query-results-cache"></a>クエリ結果キャッシュのクリア

コンテキストデータベースに対して行われたすべてのキャッシュされた[クエリ結果](../query/query-results-cache.md)をクリアします。

**構文**

`.clear` `database` `cache` `query_results`

**戻り値**

このコマンドは、次の列を含むテーブルを返します。

|Column    |種類    |説明
|---|---|---
|NodeId|`string`|クラスターノードの識別子。
|Count|`long`|ノードによって削除されたエントリの数。

**例**

```kusto
.clear database cache queryresults
```

|NodeId|エントリ|
|---|---|
|Node1|42
|Node2|0
