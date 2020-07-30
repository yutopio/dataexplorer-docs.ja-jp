---
title: parse_ipv6 ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの parse_ipv6 () 関数について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: 25ed06f738e6b2e090ff92be9df026a85a27a89f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346424"
---
# <a name="parse_ipv6"></a>parse_ipv6()

IPv6 または IPv4 文字列を正規の IPv6 文字列形式に変換します。

```kusto
parse_ipv6("127.0.0.1") == '0000:0000:0000:0000:0000:ffff:7f00:0001'
parse_ipv6(":fe80::85d:e82c:9446:7994") == 'fe80:0000:0000:0000:085d:e82c:9446:7994'
```

## <a name="syntax"></a>構文

`parse_ipv6(`*`Expr`*`)`

## <a name="arguments"></a>引数

* *`Expr`*: 正規の IPv6 表記に変換される IPv6/IPv4 ネットワークアドレスを表す文字列式。 文字列には、 [IP プレフィックス表記](#ip-prefix-notation)を使用した net マスクを含めることができます。

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
 '192.168.255.255',     32,  // 32-bit netmask is used
 '192.168.255.255/24',  30,  // 24-bit netmask is used, as IPv4 address doesn't use upper 8 bits
 '255.255.255.255',     24,  // 24-bit netmask is used
]
| extend ip_long = parse_ipv4_mask(ip_string, netmask)
```

|ip_string|ネット|ip_long|
|---|---|---|
|192.168.255.255|32|3232301055|
|192.168.255.255/24|30|3232300800|
|255.255.255.255|24|4294967040|


