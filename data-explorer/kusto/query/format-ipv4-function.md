---
title: format_ipv4 ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの format_ipv4 () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/09/2020
ms.openlocfilehash: 5f941a640ffc0bf30859509cf3e3eb1235f464ff
ms.sourcegitcommit: ed902a5a781e24e081cd85910ed15cd468a0db1e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88075406"
---
# <a name="format_ipv4"></a>format_ipv4 ()

ネットマスクを使用して入力を解析し、IPv4 アドレスを表す文字列を返します。

```kusto
print format_ipv4('192.168.1.255', 24) == '192.168.1.0'
print format_ipv4(3232236031, 24) == '192.168.1.0'
```

## <a name="syntax"></a>構文

`format_ipv4(`*Expr* [ `,` *PrefixMask*`])`

## <a name="arguments"></a>引数

* *`Expr`*: IPv4 アドレスの文字列または数値表現。
* *`PrefixMask`*: (省略可能) 考慮される最上位ビットの数を表す 0 ~ 32 の整数。 引数が指定されていない場合は、すべてのビットマスクが使用されます (32)。

## <a name="returns"></a>戻り値

変換が成功した場合、結果は IPv4 アドレスを表す文字列になります。
変換に失敗した場合、結果は空の文字列になります。

## <a name="see-also"></a>関連項目

- [format_ipv4_mask ()](format-ipv4-mask-function.md): CIDR 表記を含む ipv4 アドレス形式の場合。
- [IPv4 および IPv6 の機能](scalarfunctions.md#ipv4ipv6-functions)

## <a name="examples"></a>例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(address:string, mask:long)
[
 '192.168.1.1', 24,          
 '192.168.1.1', 32,          
 '192.168.1.1/24', 32,       
 '192.168.1.1/24', long(-1), 
]
| extend result = format_ipv4(address, mask), 
         result_mask = format_ipv4_mask(address, mask)
```

|address|mask|結果|result_mask|
|---|---|---|---|
|192.168.1.1|24|192.168.1.0|192.168.1.0/24|
|192.168.1.1|32|192.168.1.1|192.168.1.1/32|
|192.168.1.1/24|32|192.168.1.0|192.168.1.0/24|
|192.168.1.1/24|-1|||
