---
title: クエリ結果のキャッシュ-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのクエリ結果キャッシュについて説明します。
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 630266fdaaaac51037a99a6a5243db58a232ae48
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943118"
---
# <a name="query-results-cache"></a>クエリ結果のキャッシュ

クエリ結果キャッシュは、クエリ結果を格納するための専用キャッシュです。 詳細については、「[クエリ結果のキャッシュ](../query/query-results-cache.md)」を参照してください。

**クエリ結果のキャッシュコマンド**

Kusto には、キャッシュ管理と可観測性のための2つのコマンドが用意されています。

* [`Show cache`](show-query-results-cache-command.md): 結果キャッシュによって公開された統計を表示するには、このコマンドを使用します。

* [`Clear cache(rhs:string)`](clear-query-results-cache-command.md): キャッシュされた結果を消去するには、このコマンドを使用します。
