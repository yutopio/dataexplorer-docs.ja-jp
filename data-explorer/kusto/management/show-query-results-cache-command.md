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
ms.openlocfilehash: bcbdb59355ce0461d735cbea902551c219479fd2
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943105"
---
# <a name="show-query-results-cache"></a>。クエリ結果のキャッシュを表示します

[クエリ結果キャッシュ](../query/query-results-cache.md)に関連する統計を示すテーブルを返します。

**構文**

`.show` `query` `results` `cache`

**出力**
 
|出力パラメーター |型 |説明 
|---|---|---
|ヒット数  |long |キャッシュヒットの数。
|[Misses] \(ミス数)  |long |キャッシュミスの数。
|CacheCapacityInBytes |long |キャッシュ容量 (バイト単位)。
|未使用のバイト数  |long |キャッシュの使用領域。
|Count  |long | キャッシュに格納されている一意のクエリ結果の数。

**制限事項**

* 現在、コマンドの出力には、要求が到着したノードによって収集されたキャッシュ統計のみが反映されています。
* このコマンドは、"最近" の履歴のみを表示します。
