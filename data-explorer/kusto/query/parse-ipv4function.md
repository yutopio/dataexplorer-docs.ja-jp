---
title: parse_ipv4() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで parse_ipv4() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 296c7e77a2397501ae086e16154ca249249c23e4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511749"
---
# <a name="parse_ipv4"></a>parse_ipv4()

IPv4 文字列を long (符号付き 64 ビット) の数値表現に変換します。

```kusto
parse_ipv4("127.0.0.1") == 2130706433
parse_ipv4('192.1.168.1') < parse_ipv4('192.1.168.2') == true
```

**構文**

`parse_ipv4(`*Expr*`)`

**引数**

* *Expr*: 長整数型に変換される IPv4 を表す文字列式です。 文字列には[、IP プレフィックス表記](#ip-prefix-notation)を使用するネット マスクを含めることができます。

### <a name="ip-prefix-notation"></a>IP プレフィックス表記

スラッシュ ()`IP-prefix notation``/`文字を使用して IP アドレスを定義するのが一般的です。
スラッシュ (`/`) の LEFT の IP アドレスはベース IP アドレスで、スラッシュ (/) の RIGHT の番号 (1 から 32) はネットマスク内の連続する 1 ビットの数です。 したがって、192.168.2.0/24 には、24 個の連続ビットまたは 255.255.255.0 をドット付き 10 進形式で含む、関連付けられた net/subnetmask が付けられます。

**戻り値**

変換が成功すると、結果は長い数値になります。
変換が成功しなかった場合は、 が`null`返されます。
 
**例**

```kusto
datatable(ip_string:string)
[
 '192.168.1.1',
 '192.168.1.1/24',
 '255.255.255.255/31'
]
| extend ip_long = parse_ipv4(ip_string)
```

|ip_string|ip_long|
|---|---|
|192.168.1.1|3232235777|
|192.168.1.1/24|3232235776|
|255.255.255.255/31|4294967294|