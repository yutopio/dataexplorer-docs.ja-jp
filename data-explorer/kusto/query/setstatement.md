---
title: ステートメントの設定 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのステートメントの設定について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8cb9c1d72f1b2e8e4bfbbd28d67c04295c9ccf5b
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765576"
---
# <a name="set-statement"></a>set ステートメント

::: zone pivot="azuredataexplorer"

この`set`ステートメントは、クエリの実行時間に対してクエリ オプションを設定するために使用されます。
クエリ オプションは、クエリの実行方法とクエリが結果を返す方法を制御します。 ブールフラグ (既定ではオフ) にすることも、整数値を持つ場合があります。 クエリには任意の数の set ステートメントを含めることができます (含めなくてもかまいません)。 Set ステートメントは、プログラム順で後続する表形式の式ステートメントにのみ影響します。

* クエリ オプションは、`ClientRequestProperties`オブジェクトで設定することによってプログラムで有効にすることもできます。 [こちら](../api/netfx/request-properties.md)を参照してください。
  
* クエリ オプションは、正式には Kusto 言語の一部ではなく、互換性に影響を与える言語の変更と見なされることなく変更できます。

**構文**

`set`*オプション名*`=` [*オプション値*]

**例**

```kusto
set querytrace;
Events | take 100
```

::: zone-end

::: zone pivot="azuremonitor"

これは Azure モニターではサポートされていません。

::: zone-end
