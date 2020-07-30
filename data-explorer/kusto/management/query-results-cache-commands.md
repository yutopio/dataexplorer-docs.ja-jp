---
title: クエリ結果のキャッシュ-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのクエリ結果キャッシュについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: fa2bf2f6d24162c5bdb1c851ef7d74e4eb39489f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349909"
---
# <a name="query-results-cache"></a>クエリ結果のキャッシュ

クエリ結果キャッシュは、クエリ結果を格納するための専用キャッシュです。 詳細については、「[クエリ結果のキャッシュ](../query/query-results-cache.md)」を参照してください。

**クエリ結果のキャッシュコマンド**

Kusto には、キャッシュ管理と可観測性のための2つのコマンドが用意されています。

* [`Show cache`](show-query-results-cache-command.md): 結果キャッシュによって公開された統計を表示するには、このコマンドを使用します。

* [`Clear cache(rhs:string)`](clear-query-results-cache-command.md): キャッシュされた結果を消去するには、このコマンドを使用します。
