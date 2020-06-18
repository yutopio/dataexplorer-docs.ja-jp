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
ms.openlocfilehash: 9ddb94e005e88855bbeddd7d3cf8ab537a42d413
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943121"
---
# <a name="clear-query-results-cache"></a>クエリ結果キャッシュのクリア

コンテキストデータベースに対して行われたすべてのキャッシュされた[クエリ結果](../query/query-results-cache.md)をクリアします。

**構文**

`.clear` `database` `cache` `queryresults`

**戻り値**

このコマンドは、次の列を含むテーブルを返します。

|Column    |種類    |説明
|---|---|---
|NodeId|`string`|クラスターノードの識別子。
|エントリ|`long`|ノードによってクリアされたエントリの数。

**例**

```kusto
.clear database cache queryresults
```

|NodeId|エントリ|
|---|---|
|Node1|42
|Node2|0
