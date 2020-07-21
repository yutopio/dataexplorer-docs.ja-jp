---
title: クエリ結果キャッシュの表示-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでクエリ結果キャッシュを表示する方法について説明します。
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 84805cae07049af4ffa8a2fdb82e637261140f8f
ms.sourcegitcommit: cf1da667be12656a8c4727c23144421b5a4b1099
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/21/2020
ms.locfileid: "86565430"
---
# <a name="show-database-cache-query_results"></a>。データベースキャッシュ query_results を表示します

コンテキストデータベースに対して行われた[クエリ結果キャッシュ](../query/query-results-cache.md)に関連する統計を示すテーブルを返します。

**構文**

`.show database query results cache`

**出力**
 
|出力パラメーター |Type |説明 
|---|---|---
|NodeId|`string`|クラスターノードの識別子。
|ヒット数  |`long`|キャッシュヒットの数。
|[Misses] \(ミス数)  |`long`|キャッシュミスの数。
|CacheCapacityInBytes |`long` |キャッシュ容量 (バイト単位)。
|未使用のバイト数  |`long` |キャッシュの使用領域。
|Count  |`long`| キャッシュに格納されている一意のクエリ結果の数。
