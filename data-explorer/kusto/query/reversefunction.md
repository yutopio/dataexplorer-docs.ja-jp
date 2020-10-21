---
title: reverse ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの reverse () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dc348643ff6e098b2291e69d68ca817c1f5af9c2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243003"
---
# <a name="reverse"></a>reverse()

関数は、入力文字列の順序を逆にします。
入力値が型でない場合、 `string` 関数は強制的に値を型にキャストし `string` ます。

## <a name="syntax"></a>構文

`reverse(`*電源*`)`

## <a name="arguments"></a>引数

* *source*: 入力値。  

## <a name="returns"></a>戻り値

文字列値の逆の順序。

## <a name="examples"></a>例

```kusto
print str = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
| extend rstr = reverse(str)
```

|str|rstr|
|---|---|
|ABCDEFGHIJKLMNOPQRSTUVWXYZ|ZYXWVUTSRQPONMLKJIHGFEDCBA|


```kusto
print ['int'] = 12345, ['double'] = 123.45, 
['datetime'] = datetime(2017-10-15 12:00), ['timespan'] = 3h
| project rint = reverse(['int']), rdouble = reverse(['double']), 
rdatetime = reverse(['datetime']), rtimespan = reverse(['timespan'])
```

|rint|rdouble|rdatetime|rtimespan|
|---|---|---|---|
|54321|54.321|Z 0000000.00:00: 21T51-01-7102|00:00:30|
