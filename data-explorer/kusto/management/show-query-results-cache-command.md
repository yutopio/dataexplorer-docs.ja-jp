---
title: クエリ結果キャッシュの表示-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでクエリ結果キャッシュを表示する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: e037b6ed01a2eba7c7370d75a4efea0f7cbc1756
ms.sourcegitcommit: 92b8057a36bd7daa16226f1526b29253bceb3602
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2020
ms.locfileid: "91402720"
---
# <a name="show-database-cache-query_results"></a>。データベースキャッシュ query_results を表示します

コンテキストデータベースに対して行われた [クエリ結果キャッシュ](../query/query-results-cache.md) に関連する統計を示すテーブルを返します。

**構文**

`.show database cache query_results`

**出力**
 
|出力パラメーター |型 |説明 
|---|---|---
|NodeId|`string`|クラスターノードの識別子。
|ヒット数  |`long`|キャッシュヒットの数。
|[Misses] \(ミス数)  |`long`|キャッシュミスの数。
|CacheCapacityInBytes |`long` |キャッシュ容量 (バイト単位)。
|未使用のバイト数  |`long` |キャッシュの使用領域。
|Count  |`long`| キャッシュに格納されている一意のクエリ結果の数。
