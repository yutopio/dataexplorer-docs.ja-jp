---
title: ipv4_compare ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの ipv4_compare () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 68082b68a1c7772135f711248ddfcd4079bc753e
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803932"
---
# <a name="ipv4_compare"></a>ipv4_compare()

2つの IPv4 文字列を比較します。 2つの IPv4 文字列は、引数のプレフィックスから計算された IP プレフィックスの組み合わせと、省略可能な引数を考慮して、解析と比較が実行され `PrefixMask` ます。

```kusto
ipv4_compare("127.0.0.1", "127.0.0.1") == 0
ipv4_compare('192.168.1.1', '192.168.1.255') < 0
ipv4_compare('192.168.1.1/24', '192.168.1.255/24') == 0
ipv4_compare('192.168.1.1', '192.168.1.255', 24) == 0
```

## <a name="syntax"></a>構文

`ipv4_compare(`*Expr1 or* `, `*Expr2* `[ ,`*PrefixMask*`])`

## <a name="arguments"></a>引数

* *Expr1 or*, *Expr2*: IPv4 アドレスを表す文字列式。 IPv4 文字列は[、IP プレフィックス表記](#ip-prefix-notation)を使用してマスクできます。
* *PrefixMask*: 0 ~ 32 の整数で、考慮される最上位ビットの数を表します。

## <a name="ip-prefix-notation"></a>IP プレフィックスの表記
 
IP アドレスは `IP-prefix notation` 、スラッシュ () 文字を使用して定義でき `/` ます。
スラッシュ () の左側の IP アドレスは、 `/` 基本 ip アドレスです。 スラッシュ () の右側にある数字 (1 ~ 32) `/` は、ネットマスク内の連続した1ビットの数です。 

たとえば、192.168.2.0/24 は、24個の連続するビットまたは255.255.255.0 をドット形式の10進形式で含む、関連付けられた net/subnetmask を持ちます。

## <a name="returns"></a>戻り値

* `0`: 最初の IPv4 文字列引数の長い形式が2番目の IPv4 文字列引数と等しい場合
* `1`: 最初の IPv4 文字列引数の長い形式が2番目の IPv4 文字列引数より大きい場合
* `-1`: 最初の IPv4 文字列引数の長い形式が2番目の IPv4 文字列引数より小さい場合
* `null`: 2 つの IPv4 文字列のいずれかの変換が成功しなかった場合。

## <a name="examples-ipv4-comparison-equality-cases"></a>例: IPv4 比較等値ケース

### <a name="compare-ips-using-the-ip-prefix-notation-specified-inside-the-ipv4-strings"></a>IPv4 文字列内で指定された IP プレフィックス表記を使用して、IP を比較します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string)
[
 '192.168.1.0',    '192.168.1.0',       // Equal IPs
 '192.168.1.1/24', '192.168.1.255',     // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
 '192.168.1.1/30', '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_compare(ip1_string, ip2_string)
```

|ip1_string|ip2_string|結果|
|---|---|---|
|192.168.1.0|192.168.1.0|0|
|192.168.1.1/24|192.168.1.255|0|
|192.168.1.1|192.168.1.255/24|0|
|192.168.1.1/30|192.168.1.255/24|0|

### <a name="compare-ips-using-ip-prefix-notation-specified-inside-the-ipv4-strings-and-as-additional-argument-of-the-ipv4_compare-function"></a>IPv4 文字列内で指定された IP プレフィックス表記と、関数の追加引数を使用して、IP を比較します。 `ipv4_compare()`

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string, prefix:long)
[
 '192.168.1.1',    '192.168.1.0',   31, // 31 bit IP-prefix is used for comparison
 '192.168.1.1/24', '192.168.1.255', 31, // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255', 24, // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_compare(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|結果|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|0|
|192.168.1.1/24|192.168.1.255|31|0|
|192.168.1.1|192.168.1.255|24|0|

