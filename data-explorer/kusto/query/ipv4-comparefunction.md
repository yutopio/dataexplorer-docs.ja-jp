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
ms.openlocfilehash: 9fec1869ee06e4fd9a9932e42c6ab1049b50a04f
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271504"
---
# <a name="ipv4_compare"></a>ipv4_compare ()

2つの IPv4 文字列を比較します。

```kusto
ipv4_compare("127.0.0.1", "127.0.0.1") == 0
ipv4_compare('192.168.1.1', '192.168.1.255') < 0
ipv4_compare('192.168.1.1/24', '192.168.1.255/24') == 0
ipv4_compare('192.168.1.1', '192.168.1.255', 24) == 0
```

**構文**

`ipv4_compare(`*Expr1 or* `, `*Expr2* `[ ,`*PrefixMask*`])`

**引数**

* *Expr1 or*, *Expr2*: IPv4 アドレスを表す文字列式。 IPv4 文字列は[、IP プレフィックス表記](#ip-prefix-notation)を使用してマスクできます。
* *PrefixMask*: 0 ~ 32 の整数で、考慮される最上位ビットの数を表します。

### <a name="ip-prefix-notation"></a>IP プレフィックスの表記

一般的な方法では、スラッシュ () 文字を使用して IP アドレスを定義し `IP-prefix notation` `/` ます。
スラッシュ () の左側の IP アドレス `/` は基本 ip アドレスで、スラッシュ () の右側にある番号 (1 ~ 32) `/` は、ネットマスクの連続した1ビット数です。 

例: 192.168.2.0/24 には、24個の連続するビットまたは255.255.255.0 をドット形式の10進形式で含む、関連付けられた net/subnetmask があります。

**戻り値**

2つの IPv4 文字列は、引数のプレフィックスから計算された IP プレフィックスの組み合わせと、省略可能な引数を考慮して、解析と比較が実行され `PrefixMask` ます。

戻り値:
* `0`: 最初の IPv4 文字列引数の長い形式が2番目の IPv4 文字列引数と等しい場合
* `1`: 最初の IPv4 文字列引数の長い形式が2番目の IPv4 文字列引数より大きい場合
* `-1`: 最初の IPv4 文字列引数の長い形式が2番目の IPv4 文字列引数より小さい場合

2つの IPv4 文字列のいずれかの変換が成功しなかった場合、結果はになり `null` ます。

## <a name="examples-ipv4-comparison-equality-cases"></a>例: IPv4 比較等値ケース

次の例では、IPv4 文字列内で指定された IP プレフィックス表記を使用して、さまざまな ip を比較します。

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

次の例では、IPv4 文字列内で指定された IP プレフィックス表記と、関数の追加引数を使用して、さまざまな ip を比較し `ipv4_compare()` ます。

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
