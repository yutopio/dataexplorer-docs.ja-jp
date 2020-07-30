---
title: parse_ipv4_mask ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの parse_ipv6_mask () 関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: a6c17f0505927c38d26c37a5e9872747541d129a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346441"
---
# <a name="parse_ipv6_mask"></a>parse_ipv6_mask()
 
IPv6/IPv4 文字列とネットマスクを正規の IPv6 文字列形式に変換します。

```kusto
parse_ipv6_mask("127.0.0.1", 24) == '0000:0000:0000:0000:0000:ffff:7f00:0000'
parse_ipv6_mask(":fe80::85d:e82c:9446:7994", 120) == 'fe80:0000:0000:0000:085d:e82c:9446:7900'
```

## <a name="syntax"></a>構文

`parse_ipv6_mask(`*`Expr`*`, `*`PrefixMask`*`)`

## <a name="arguments"></a>引数

* *`Expr`*: 正規の IPv6 表記に変換される IPv6/IPv4 ネットワークアドレスを表す文字列式。 文字列には、 [IP プレフィックス表記](#ip-prefix-notation)を使用した net マスクを含めることができます。
* *`PrefixMask`*: 考慮される最上位ビットの数を表す 0 ~ 128 の整数。

## <a name="ip-prefix-notation"></a>IP プレフィックスの表記

IP アドレスは `IP-prefix notation` 、スラッシュ () 文字を使用して定義でき `/` ます。
スラッシュ () の左側の IP アドレスは、 `/` 基本 ip アドレスです。 スラッシュ () の右側にある数字 (1 ~ 127) `/` は、ネットマスク内の連続した1ビットの数です。

## <a name="returns"></a>戻り値

変換が成功した場合、結果は正規の IPv6 ネットワークアドレスを表す文字列になります。
変換に失敗した場合、結果はになり `null` ます。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip_string:string, netmask:long)
[
 // IPv4 addresses
 '192.168.255.255',     120,  // 120-bit netmask is used
 '192.168.255.255/24',  124,  // 120-bit netmask is used, as IPv4 address doesn't use upper 8 bits
 '255.255.255.255', 128,  // 128-bit netmask is used
 // IPv6 addresses
 'fe80::85d:e82c:9446:7994', 128,     // 128-bit netmask is used
 'fe80::85d:e82c:9446:7994/120', 124, // 120-bit netmask is used
 // IPv6 with IPv4 notation
 '::192.168.255.255',    128,  // 128-bit netmask is used
 '::192.168.255.255/24', 128,  // 120-bit netmask is used, as IPv4 address doesn't use upper 8 bits
]
| extend ip6_canonical = parse_ipv6_mask(ip_string, netmask)
```

|ip_string|ネット|ip6_canonical|
|---|---|---|
|192.168.255.255|120|0000: 0000: 0000: 0000: 0000: ffff: c0a8: ff00|
|192.168.255.255/24|124|0000: 0000: 0000: 0000: 0000: ffff: c0a8: ff00|
|255.255.255.255|128|0000: 0000: 0000: 0000: 0000: ffff: ffff: ffff|
|fe80:: 85d: e82c: 9446: 7994|128|fe80: 0000: 0000: 0000: 085d: e82c: 9446: 7994|
|fe80:: 85d: e82c: 9446: 7994/120|124|fe80: 0000: 0000: 0000: 085d: e82c: 9446: 7900|
|:: 192.168.255.255|128|0000: 0000: 0000: 0000: 0000: ffff: c0a8: ffff|
|:: 192.168.255.255/24|128|0000: 0000: 0000: 0000: 0000: ffff: c0a8: ff00|

