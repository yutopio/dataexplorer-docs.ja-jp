---
title: parse_ipv4_mask ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの parse_ipv4_mask () 関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: fd6bd97befc376d13573a0a85169524cf01b9d2d
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294612"
---
# <a name="parse_ipv4_mask"></a>parse_ipv4_mask()

IPv4 とネットマスクの入力文字列を long 数値表現 (符号付き64ビット) に変換します。

```kusto
parse_ipv4_mask("127.0.0.1", 24) == 2130706432
parse_ipv4_mask('192.1.168.2', 31) == parse_ipv4_mask('192.1.168.3', 31)
```

**構文**

`parse_ipv4_mask(`*`Expr`*`, `*`PrefixMask`*`)`

**引数**

* *`Expr`*: Long 型に変換される IPv4 アドレスの文字列表現。 
* *`PrefixMask`*: 考慮される最上位ビットの数を表す 0 ~ 32 の整数。

**戻り値**

変換が成功した場合、結果は長い数値になります。
変換に失敗した場合、結果はになり `null` ます。
