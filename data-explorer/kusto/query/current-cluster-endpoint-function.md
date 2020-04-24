---
title: current_cluster_endpoint() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでcurrent_cluster_endpoint() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 65ce130b4dd3e0a3125eefc6c410775647f9b964
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516849"
---
# <a name="current_cluster_endpoint"></a>current_cluster_endpoint()

照会されている現在のクラスターのネットワーク エンドポイント (DNS 名) を返します。

**構文**

`current_cluster_endpoint()`

**戻り値**

クエリを実行している現在のクラスタのネットワーク エンドポイント (DNS 名) の値`string`として type を指定します。

**例**

```kusto
print strcat("This query executed on: ", current_cluster_endpoint())
```