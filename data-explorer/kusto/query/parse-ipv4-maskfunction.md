---
title: parse_ipv4_mask() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでparse_ipv4_mask() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 994c1e551d7c38bb1493579bcf10438acd2923f2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511783"
---
# <a name="parse_ipv4_mask"></a>parse_ipv4_mask()

IPv4 とネットマスクの入力文字列を long 形式 (符号付き 64 ビット) に変換します。

```kusto
parse_ipv4_mask("127.0.0.1", 24) == 2130706432

parse_ipv4_mask('192.1.168.2', 31) == parse_ipv4_mask('192.1.168.3', 31) 
```

**構文**

`parse_ipv4_mask(`*エクスプル*`, `*プレフィックスマスク*`)`

**引数**

* *Expr*: long に変換される IPv4 アドレスの文字列表現。 
* *PrefixMask*: 考慮される最上位ビットの数を表す 0 から 32 までの整数。

**戻り値**

変換が成功すると、結果は長い数値になります。
変換が成功しなかった場合、結果は`null`になります。