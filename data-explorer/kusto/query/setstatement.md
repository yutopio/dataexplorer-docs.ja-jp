---
title: Set ステートメント-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの Set ステートメントについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 028bfb5a2d0ddf25f65cd16bca2c498d9dcb7059
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737863"
---
# <a name="set-statement"></a>set ステートメント

::: zone pivot="azuredataexplorer"

`set`ステートメントは、クエリの実行中にクエリオプションを設定するために使用されます。
クエリ オプションは、クエリの実行方法とクエリが結果を返す方法を制御します。 ブール型のフラグ (既定ではオフ)、または整数値を持つことができます。 クエリには任意の数の set ステートメントを含めることができます (含めなくてもかまいません)。 Set ステートメントは、プログラムの順序で記録される表形式の式ステートメントにのみ影響します。

* クエリオプションは、 `ClientRequestProperties`プログラムでオブジェクトに設定することによって有効にすることもできます。 [こちら](../api/netfx/request-properties.md)を参照してください。
  
* クエリオプションは、正式には Kusto 言語の一部ではなく、互換性のある言語の変更と見なされずに変更される可能性があります。

**構文**

`set`*OptionName* [`=` *OptionValue*]

**例**

```kusto
set querytrace;
Events | take 100
```

::: zone-end

::: zone pivot="azuremonitor"

この機能は、ではサポートされていません Azure Monitor

::: zone-end
