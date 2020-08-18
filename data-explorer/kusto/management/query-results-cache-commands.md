---
title: クエリ結果のキャッシュコマンド-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのクエリ結果キャッシュについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: amitof
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
ms.openlocfilehash: 519560f0b6bb98651dc4a8b6749935ec9843eb9c
ms.sourcegitcommit: 5e903c61e779f7bf62f745f13a6038ce2a32e934
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88512576"
---
# <a name="query-results-cache-commands"></a>クエリ結果のキャッシュコマンド

クエリ結果キャッシュは、クエリ結果を格納するための専用キャッシュです。 詳細については、「 [クエリ結果のキャッシュ](../query/query-results-cache.md)」を参照してください。

**クエリ結果のキャッシュコマンド**

Kusto には、キャッシュ管理と可観測性のための2つのコマンドが用意されています。

* [`Show cache`](show-query-results-cache-command.md): 結果キャッシュによって公開された統計を表示するには、このコマンドを使用します。

* [`Clear cache(rhs:string)`](clear-query-results-cache-command.md): キャッシュされた結果を消去するには、このコマンドを使用します。
