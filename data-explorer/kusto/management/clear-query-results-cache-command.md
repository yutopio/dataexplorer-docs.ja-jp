---
title: クエリ結果のキャッシュのクリア-Azure データエクスプローラー
description: Azure データエクスプローラーでキャッシュされたクエリの結果をクリアする方法について説明します。 使用するコマンドについて説明し、例を示します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 72678453211ada2a6366b771153eeb11a717d7a3
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002952"
---
# <a name="clear-query-results-cache"></a>クエリ結果キャッシュのクリア

コンテキストデータベースに対して行われたすべてのキャッシュされた [クエリ結果](../query/query-results-cache.md) をクリアします。

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
