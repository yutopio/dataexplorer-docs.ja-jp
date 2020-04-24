---
title: ipv4_compare() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで ipv4_compare() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: ddaaa4e1f9dcf9dfbcd55406cd26e8f1ff4a02fa
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513687"
---
# <a name="ipv4_compare"></a>ipv4_compare()

2 つの IPv4 文字列を比較します。

```kusto
ipv4_compare("127.0.0.1", "127.0.0.1") == 0
ipv4_compare('192.168.1.1', '192.168.1.255') < 0
ipv4_compare('192.168.1.1/24', '192.168.1.255/24') == 0
ipv4_compare('192.168.1.1', '192.168.1.255', 24) == 0
```

**構文**

`ipv4_compare(`*Expr1*`, `*Expr2*`[ ,`*プレフィックスマスク*`])`

**引数**

* *Expr1* *、Expr2*: IPv4 アドレスを表す文字列式。 [Ip プレフィックス表記](#ip-prefix-notation)を使用して IPv4 文字列をマスクできます。
* *PrefixMask*: 考慮される最上位ビットの数を表す 0 から 32 までの整数。

### <a name="ip-prefix-notation"></a>IP プレフィックス表記

スラッシュ ()`IP-prefix notation``/`文字を使用して IP アドレスを定義するのが一般的です。
スラッシュ (`/`) の LEFT の IP アドレスはベース IP アドレスで、スラッシュの`/`RIGHT の番号 (1 から 32) はネットマスク内の連続する 1 ビットの数です。 

例: 192.168.2.0/24 には、24 個の連続ビットまたは 255.255.255.0 をドット付き 10 進形式で含む、関連付けられた net/subnetmask があります。

**戻り値**

2 つの IPv4 文字列は、引数プレフィックスから計算された IP プレフィックス マスクの組み合わせとオプション`PrefixMask`の引数を考慮しながら解析および比較されます。

戻り値:
* `0`: 最初の IPv4 文字列引数の long 表現が 2 番目の IPv4 文字列引数と等しい場合
* `1`: 最初の IPv4 文字列引数の long 表現が 2 番目の IPv4 文字列引数より大きい場合
* `-1`: 最初の IPv4 文字列引数の long 表現が 2 番目の IPv4 文字列引数より小さい場合

2 つの IPv4 文字列のうち 1 つの変換が成功しなかった場合`null`、結果は .

## <a name="examples-ipv4-comparison-equality-cases"></a>例: IPv4 比較等価のケース

次の例では、IPv4 文字列内で指定された IP プレフィックス表記を使用して、さまざまな IP を比較します。

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

次の例では、IPv4 文字列内で指定された IP プレフィックス表記法を使用し、関数の追加`ipv4_compare()`引数としてさまざまな IP を比較します。

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