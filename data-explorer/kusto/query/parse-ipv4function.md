---
title: parse_ipv4 ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの parse_ipv4 () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 48bfab2549da572efba117c21d783b35ac0202af
ms.sourcegitcommit: 6f610cd9c56dbfaff4eb0470ac0d1441211ae52d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2020
ms.locfileid: "91954724"
---
# <a name="parse_ipv4"></a>parse_ipv4()

IPv4 文字列を long (符号付き64ビット) 数値表現に変換します。

```kusto
parse_ipv4("127.0.0.1") == 2130706433
parse_ipv4('192.1.168.1') < parse_ipv4('192.1.168.2') == true
```

## <a name="syntax"></a>構文

`parse_ipv4(`*`Expr`*`)`

## <a name="arguments"></a>引数

* *`Expr`*: Long 型に変換される IPv4 を表す文字列式。 文字列には、 [IP プレフィックス表記](#ip-prefix-notation)を使用した net マスクを含めることができます。

## <a name="ip-prefix-notation"></a>IP プレフィックスの表記

IP アドレスは `IP-prefix notation` 、スラッシュ () 文字を使用して定義でき `/` ます。
スラッシュ () の左側の IP アドレスは、 `/` 基本 ip アドレスです。 スラッシュ (/) の右側にある数字 (1 ~ 32) は、ネットマスク内の連続した1ビットの数です。

たとえば、192.168.2.0/24 は、24個の連続するビットまたは255.255.255.0 をドット形式の10進形式で含む、関連付けられた net/subnetmask を持ちます。

## <a name="returns"></a>戻り値

変換が成功した場合、結果は長い数値になります。
変換に失敗した場合、結果はになり `null` ます。
 
## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net/Samples -->
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
