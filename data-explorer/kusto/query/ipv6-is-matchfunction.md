---
title: ipv6_is_match ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの ipv6_is_match () 関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: b6d76f8ed834ec40c53321644e5cd9b7f5f93168
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347308"
---
# <a name="ipv6_is_match"></a>ipv6_is_match()

2つの IPv6 または IPv4 ネットワークアドレス文字列を照合します。 2つの IPv6/IPv4 文字列は、引数のプレフィックスから計算された IP プレフィックスマスクとオプションの引数を考慮して、解析と比較を行い `PrefixMask` ます。

```kusto
ipv6_is_match('::ffff:7f00:1', '127.0.0.1') == true
ipv6_is_match('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995') == false
ipv6_is_match('192.168.1.1/24', '192.168.1.255/24') == true
ipv6_is_match('fe80::85d:e82c:9446:7994/127', 'fe80::85d:e82c:9446:7995/127') == true
ipv6_is_match('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995', 127) == true
```

## <a name="syntax"></a>構文

`ipv6_is_match(`*Expr1 or* `, `*Expr2* `[ ,`*PrefixMask*`])`

## <a name="arguments"></a>引数

* *Expr1 or*, *Expr2*: IPv6 または IPv4 アドレスを表す文字列式。 IPv6 および IPv4 文字列は、 [IP プレフィックス表記](#ip-prefix-notation)を使用してマスクできます。
* *PrefixMask*: 0 ~ 128 の整数で、考慮される最上位ビットの数を表します。

## <a name="ip-prefix-notation"></a>IP プレフィックスの表記
 
IP アドレスは `IP-prefix notation` 、スラッシュ () 文字を使用して定義でき `/` ます。
スラッシュ () の左側の IP アドレスは、 `/` 基本 ip アドレスです。 スラッシュ () の右側にある数字 (1 ~ 127) `/` は、ネットマスク内の連続した1ビットの数です。 

## <a name="example"></a>例:
fe80:: 85d: e82c: 9446: 7994/120 には、120連続ビットを含む、関連付けられた net/subnetmask があります。

## <a name="returns"></a>戻り値

* `true`: 最初の IPv6/IPv4 文字列引数の長い形式が2番目の IPv6/IPv4 文字列引数と等しい場合は。
* `false`それ以外.
* `null`: 2 つの IPv6/IPv4 文字列のいずれかの変換が成功しなかった場合。

> [!Note]
> 関数は、IPv6 と IPv4 の両方のネットワークアドレスを表す引数を受け入れて比較できます。 呼び出し元が引数が IPv4 形式であることを認識している場合は、 [ipv4_is_match ()](./ipv4-is-matchfunction.md)関数を使用します。 この関数を実行すると、実行時のパフォーマンスが向上します。

## <a name="examples"></a>例

### <a name="ipv6ipv4-comparison-equality-case---ip-prefix-notation-specified-inside-the-ipv6ipv4-strings"></a>Ipv6/IPv4 比較等値大文字小文字の区別-IPv6/IPv4 文字列内で指定された IP プレフィックス表記

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
| extend result = ipv6_is_match(ip1_string, ip2_string)
```

|ip1_string|ip2_string|結果|
|---|---|---|
|192.168.1.1|192.168.1.1|1|
|192.168.1.1/24|192.168.1.255|1|
|192.168.1.1|192.168.1.255/24|1|
|192.168.1.1/30|192.168.1.255/24|1|
|fe80:: 85d: e82c: 9446: 7994|fe80:: 85d: e82c: 9446: 7994|1|
|fe80:: 85d: e82c: 9446: 7994/120|fe80:: 85d: e82c: 9446: 7998|1|
|fe80:: 85d: e82c: 9446: 7994|fe80:: 85d: e82c: 9446: 7998/120|1|
|fe80:: 85d: e82c: 9446: 7994/120|fe80:: 85d: e82c: 9446: 7998/120|1|
|192.168.1.1|:: ffff: c0a8: 0101|1|
|192.168.1.1/24|:: ffff: c0a8: 01ff|1|
|:: ffff: c0a8: 0101|192.168.1.255/24|1|
|:: 192.168.1.1/30|192.168.1.255/24|1|


### <a name="ipv6ipv4-comparison-equality-case--ip-prefix-notation-specified-inside-the-ipv6ipv4-strings-and-as-additional-argument-of-the-ipv6_is_match-function"></a>Ipv6/ipv4 比較等値大文字小文字の区別-IPv6/IPv4 文字列内で指定された IP プレフィックス表記および関数の追加引数 `ipv6_is_match()`

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
| extend result = ipv6_is_match(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|結果|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|1|
|192.168.1.1/24|192.168.1.255|31|1|
|192.168.1.1|192.168.1.255|24|1|
|fe80:: 85d: e82c: 9446: 7994|fe80:: 85d: e82c: 9446: 7995|127|1|
|fe80:: 85d: e82c: 9446: 7994/127|fe80:: 85d: e82c: 9446: 7998|120|1|
|fe80:: 85d: e82c: 9446: 7994/120|fe80:: 85d: e82c: 9446: 7998|127|1|
|192.168.1.1/24|:: ffff: c0a8: 01ff|127|1|
|:: ffff: c0a8: 0101|192.168.1.255|120|1|
|:: 192.168.1.1/30|192.168.1.255/24|127|1|
