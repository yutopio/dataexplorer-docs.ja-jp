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
ms.openlocfilehash: 7b7a96a01a4ec2b6c84609b2f9c518637d174390
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349892"
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
