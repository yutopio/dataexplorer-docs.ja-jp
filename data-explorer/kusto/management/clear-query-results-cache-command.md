---
title: クエリ結果のキャッシュのクリア-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでキャッシュされたデータベーススキーマをクリアするための管理コマンドについて説明します。
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 68d6493176696f0241467303f166b8c7160859b7
ms.sourcegitcommit: cf1da667be12656a8c4727c23144421b5a4b1099
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/21/2020
ms.locfileid: "86565438"
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
