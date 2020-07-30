---
title: reverse ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでの reverse () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fa87dae85b158e162cea90c276057abb210ea23b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345778"
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
