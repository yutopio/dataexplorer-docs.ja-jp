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
ms.openlocfilehash: 1f4213af91cfee667fdfc87b2d352fe42df3f915
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346458"
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

## <a name="example"></a>例

192.168.2.0/24 には、24個の連続するビットまたは255.255.255.0 をドット形式の10進形式で含む、関連付けられた net/subnetmask があります。

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
