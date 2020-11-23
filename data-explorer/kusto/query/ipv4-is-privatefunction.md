---
title: ipv4_is_private ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの ipv4_is_private () 関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/20/2020
ms.openlocfilehash: 161e0a2ca94bb9b037fc619c0fc5dfbddd8d28da
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324916"
---
# <a name="ipv4_is_private"></a>ipv4_is_private ()

IPv4 文字列アドレスがプライベートネットワーク Ip のセットに属しているかどうかを確認します。

[プライベートネットワークアドレス](https://en.wikipedia.org/wiki/Private_network) は、IPv4 アドレスの枯渇を遅らせるために最初に定義されています。 プライベート IP アドレスから発信またはアドレス指定された IP パケットは、パブリックインターネット経由でルーティングすることはできません。

## <a name="private-ipv4-addresses"></a>プライベート IPv4 アドレス

インターネット技術標準化委員会 (IETF) は、次の IPv4 アドレス範囲をプライベートネットワーク用に予約するように、インターネット割り当て番号機関 (IANA) に指示しました。

| IP アドレスの範囲|アドレスの数|最大 CIDR ブロック (サブネットマスク)|
|-----------------|-------------------|--------------------------------|
|10.0.0.0 – 10.255.255.255|16777216|10.0.0.0/8 (255.0.0.0)|
|172.16.0.0 – 172.31.255.255|1048576|172.16.0.0/12 (255.240.0.0)|
|192.168.0.0 – 192.168.255.255|65536|192.168.0.0/16 (255.255.0.0)|


```kusto
ipv4_is_private('192.168.1.1/24') == true
ipv4_is_private('10.1.2.3/24') == true
ipv4_is_private('202.1.2.3') == false
ipv4_is_private("127.0.0.1") == false
```

## <a name="syntax"></a>Syntax

`ipv4_is_private(`*With*`)`

## <a name="arguments"></a>引数

*Expr*: IPv4 アドレスを表す文字列式。 IPv4 文字列は [、IP プレフィックス表記](#ip-prefix-notation)を使用してマスクできます。

### <a name="ip-prefix-notation"></a>IP プレフィックスの表記

IP アドレスは `IP-prefix notation` 、スラッシュ () 文字を使用して定義でき `/` ます。 スラッシュ () の左側の IP アドレスは、 `/` 基本 ip アドレスです。 スラッシュ () の右側にある数字 (1 ~ 32) `/` は、ネットマスク内の連続した1ビットの数です。 

たとえば、192.168.2.0/24 は、24個の連続するビットまたは255.255.255.0 をドット形式の10進形式で含む、関連付けられた net/subnetmask を持ちます。

## <a name="returns"></a>戻り値

* `true`: IPv4 アドレスがプライベートネットワークの範囲のいずれかに属している場合。
*  `false`それ以外.
* `null`: 2 つの IPv4 文字列のいずれかの変換が成功しなかった場合。

## <a name="example-check-if-ipv4-belongs-to-a-private-network"></a>例: IPv4 がプライベートネットワークに属しているかどうかを確認する

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip_string:string)
[
 '10.1.2.3',
 '192.168.1.1/24',
 '127.0.0.1',
]
| extend result = ipv4_is_private(ip_string)
```

|ip_string|結果|
|---|---|
|10.1.2.3|1|
|192.168.1.1/24|1|
|127.0.0.1|0|
