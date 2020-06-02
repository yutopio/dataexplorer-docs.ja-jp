---
title: ipv4_is_match ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの ipv4_is_match () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 7b076f9878828ca8503c808b6ab94daf3375e2d4
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294629"
---
# <a name="ipv4_is_match"></a>ipv4_is_match ()

2つの IPv4 文字列を一致と見なします。 2つの IPv4 文字列は、引数のプレフィックスから計算された IP プレフィックスの組み合わせと、省略可能な引数を考慮して、解析と比較が実行され `PrefixMask` ます。

```kusto
ipv4_is_match("127.0.0.1", "127.0.0.1") == true
ipv4_is_match('192.168.1.1', '192.168.1.255') == false
ipv4_is_match('192.168.1.1/24', '192.168.1.255/24') == true
ipv4_is_match('192.168.1.1', '192.168.1.255', 24) == true
```

**構文**

`ipv4_is_match(`*Expr1 or* `, `*Expr2* `[ ,`*PrefixMask*`])`

**引数**

* *Expr1 or*, *Expr2*: IPv4 アドレスを表す文字列式。 IPv4 文字列は[、IP プレフィックス表記](#ip-prefix-notation)を使用してマスクできます。
* *PrefixMask*: 0 ~ 32 の整数で、考慮される最上位ビットの数を表します。

## <a name="ip-prefix-notation"></a>IP プレフィックスの表記

IP アドレスは `IP-prefix notation` 、スラッシュ () 文字を使用して定義でき `/` ます。 スラッシュ () の左側の IP アドレスは、 `/` 基本 ip アドレスです。 スラッシュ () の右側にある数字 (1 ~ 32) `/` は、ネットマスク内の連続した1ビットの数です。 
**例:** 192.168.2.0/24 には、24個の連続するビットまたは255.255.255.0 をドット形式の10進形式で含む、関連付けられた net/subnetmask があります。

**戻り値**

* `true`: 最初の IPv4 文字列引数の長い形式が2番目の IPv4 文字列引数と等しい場合は。
*  `false`それ以外.
* `null`: 2 つの IPv4 文字列のいずれかの変換が成功しなかった場合。

## <a name="examples"></a>例

### <a name="ipv4-comparison-equality---ip-prefix-notation-specified-inside-the-ipv4-strings"></a>Ipv4 文字列内で指定された IPv4 比較等値 IP プレフィックス表記。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string)
[
 '192.168.1.0',    '192.168.1.0',       // Equal IPs
 '192.168.1.1/24', '192.168.1.255',     // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
 '192.168.1.1/30', '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_is_match(ip1_string, ip2_string)
```

|ip1_string|ip2_string|結果|
|---|---|---|
|192.168.1.0|192.168.1.0|1|
|192.168.1.1/24|192.168.1.255|1|
|192.168.1.1|192.168.1.255/24|1|
|192.168.1.1/30|192.168.1.255/24|1|

### <a name="ipv4-comparison-equality---ip-prefix-notation-specified-inside-the-ipv4-strings-and-an-additional-argument-of-the-ipv4_is_match-function"></a>Ipv4 比較等値-IPv4 文字列内で指定された IP プレフィックス表記と関数の追加引数 `ipv4_is_match()`

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string, prefix:long)
[
 '192.168.1.1',    '192.168.1.0',   31, // 31 bit IP-prefix is used for comparison
 '192.168.1.1/24', '192.168.1.255', 31, // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255', 24, // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_is_match(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|結果|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|1|
|192.168.1.1/24|192.168.1.255|31|1|
|192.168.1.1|192.168.1.255|24|1|
