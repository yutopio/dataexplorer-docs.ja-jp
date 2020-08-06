---
title: ipv6_compare ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの ipv6_compare () 関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: ce14c466d927dd2431ae8e057bceec0d5fdd38b8
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803898"
---
# <a name="ipv6_compare"></a>ipv6_compare()

2つの IPv6 または IPv4 ネットワークアドレス文字列を比較します。 2つの IPv6 文字列は、引数のプレフィックスから計算された IP プレフィックスの組み合わせと、省略可能な引数を考慮して、解析と比較が実行され `PrefixMask` ます。

```kusto
ipv6_compare('::ffff:7f00:1', '127.0.0.1') == 0
ipv6_compare('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995')  < 0
ipv6_compare('192.168.1.1/24', '192.168.1.255/24') == 0
ipv6_compare('fe80::85d:e82c:9446:7994/127', 'fe80::85d:e82c:9446:7995/127') == 0
ipv6_compare('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995', 127) == 0
```

> [!Note]
> 関数は、IPv6 と IPv4 の両方のネットワークアドレスを表す引数を受け入れて比較できます。 ただし、呼び出し元が引数が IPv4 形式であることを認識している場合は、 [ipv4_is_compare ()](./ipv4-comparefunction.md)関数を使用します。 この関数を実行すると、実行時のパフォーマンスが向上します。

## <a name="syntax"></a>構文

`ipv6_compare(`*Expr1 or* `, `*Expr2* `[ ,`*PrefixMask*`])`

## <a name="arguments"></a>引数

* *Expr1 or*, *Expr2*: IPv6 または IPv4 アドレスを表す文字列式。 IPv6 および IPv4 文字列は、IP プレフィックス表記を使用してマスクできます (注を参照)。
* *PrefixMask*: 0 ~ 128 の整数で、考慮される最上位ビットの数を表します。

## <a name="ip-prefix-notation"></a>IP プレフィックスの表記

一般的な方法で `IP-prefix notation` は、スラッシュ () 文字を使用して IP アドレスを定義し `/` ます。
スラッシュ () の左側の IP アドレス `/` は基本 ip アドレスで、スラッシュ () の右側にある番号 (1 ~ 127) `/` は、ネットマスクの連続した1ビット数です。 

たとえば、fe80:: 85d: e82c: 9446: 7994/120 には、120連続ビットを含む、関連付けられた net/subnetmask があります。

## <a name="returns"></a>戻り値

* `0`: 最初の IPv6 文字列引数の長い形式が2番目の IPv6 文字列引数と等しい場合は。
* `1`: 最初の IPv6 文字列引数の長い形式が2番目の IPv6 文字列引数より大きい場合。
* `-1`: 最初の IPv6 文字列引数の長い形式が2番目の IPv6 文字列引数より小さい場合。
* `null`: 2 つの IPv6 文字列のいずれかの変換が成功しなかった場合。

## <a name="examples-ipv6ipv4-comparison-equality-cases"></a>例: IPv6/IPv4 比較等値ケース

### <a name="compare-ips-using-the-ip-prefix-notation-specified-inside-the-ipv6ipv4-strings"></a>IPv6/IPv4 文字列内で指定された IP プレフィックス表記を使用して、IP を比較します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string)
[
 // IPv4 are compared as IPv6 addresses
 '192.168.1.1',    '192.168.1.1',       // Equal IPs
 '192.168.1.1/24', '192.168.1.255',     // 24 bit IP4-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255/24',  // 24 bit IP4-prefix is used for comparison
 '192.168.1.1/30', '192.168.1.255/24',  // 24 bit IP4-prefix is used for comparison
  // IPv6 cases
 'fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7994',         // Equal IPs
 'fe80::85d:e82c:9446:7994/120', 'fe80::85d:e82c:9446:7998',     // 120 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7998/120',     // 120 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994/120', 'fe80::85d:e82c:9446:7998/120', // 120 bit IP6-prefix is used for comparison
 // Mixed case of IPv4 and IPv6
 '192.168.1.1',      '::ffff:c0a8:0101', // Equal IPs
 '192.168.1.1/24',   '::ffff:c0a8:01ff', // 24 bit IP-prefix is used for comparison
 '::ffff:c0a8:0101', '192.168.1.255/24', // 24 bit IP-prefix is used for comparison
 '::192.168.1.1/30', '192.168.1.255/24', // 24 bit IP-prefix is used for comparison
]
| extend result = ipv6_compare(ip1_string, ip2_string)
```

|ip1_string|ip2_string|結果|
|---|---|---|
|192.168.1.1|192.168.1.1|0|
|192.168.1.1/24|192.168.1.255|0|
|192.168.1.1|192.168.1.255/24|0|
|192.168.1.1/30|192.168.1.255/24|0|
|fe80:: 85d: e82c: 9446: 7994|fe80:: 85d: e82c: 9446: 7994|0|
|fe80:: 85d: e82c: 9446: 7994/120|fe80:: 85d: e82c: 9446: 7998|0|
|fe80:: 85d: e82c: 9446: 7994|fe80:: 85d: e82c: 9446: 7998/120|0|
|fe80:: 85d: e82c: 9446: 7994/120|fe80:: 85d: e82c: 9446: 7998/120|0|
|192.168.1.1|:: ffff: c0a8: 0101|0|
|192.168.1.1/24|:: ffff: c0a8: 01ff|0|
|:: ffff: c0a8: 0101|192.168.1.255/24|0|
|:: 192.168.1.1/30|192.168.1.255/24|0|

### <a name="compare-ips-using-ip-prefix-notation-specified-inside-the-ipv6ipv4-strings-and-as-additional-argument-of-the-ipv6_compare-function"></a>IPv6/IPv4 文字列内で指定された IP プレフィックス表記と、関数の追加引数を使用して、IP を比較します。 `ipv6_compare()`

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string, prefix:long)
[
 // IPv4 are compared as IPv6 addresses 
 '192.168.1.1',    '192.168.1.0',   31, // 31 bit IP4-prefix is used for comparison
 '192.168.1.1/24', '192.168.1.255', 31, // 24 bit IP4-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255', 24, // 24 bit IP4-prefix is used for comparison
   // IPv6 cases
 'fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995',     127, // 127 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994/127', 'fe80::85d:e82c:9446:7998', 120, // 120 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994/120', 'fe80::85d:e82c:9446:7998', 127, // 120 bit IP6-prefix is used for comparison
 // Mixed case of IPv4 and IPv6
 '192.168.1.1/24',   '::ffff:c0a8:01ff', 127, // 127 bit IP6-prefix is used for comparison
 '::ffff:c0a8:0101', '192.168.1.255',    120, // 120 bit IP6-prefix is used for comparison
 '::192.168.1.1/30', '192.168.1.255/24', 127, // 120 bit IP6-prefix is used for comparison
]
| extend result = ipv6_compare(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|結果|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|0|
|192.168.1.1/24|192.168.1.255|31|0|
|192.168.1.1|192.168.1.255|24|0|
|fe80:: 85d: e82c: 9446: 7994|fe80:: 85d: e82c: 9446: 7995|127|0|
|fe80:: 85d: e82c: 9446: 7994/127|fe80:: 85d: e82c: 9446: 7998|120|0|
|fe80:: 85d: e82c: 9446: 7994/120|fe80:: 85d: e82c: 9446: 7998|127|0|
|192.168.1.1/24|:: ffff: c0a8: 01ff|127|0|
|:: ffff: c0a8: 0101|192.168.1.255|120|0|
|:: 192.168.1.1/30|192.168.1.255/24|127|0|

