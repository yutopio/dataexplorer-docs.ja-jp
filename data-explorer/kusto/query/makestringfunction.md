---
title: make_string ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの make_string () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c8f3497e10c15bfd6df0337758d8dc3002419fa1
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252237"
---
# <a name="make_string"></a>make_string()

Unicode 文字によって生成された文字列を返します。
    
## <a name="syntax"></a>構文

`make_string (`*Arg1*[, *ArgN*]... `)`

## <a name="arguments"></a>引数

* *Arg1* ... *ArgN*: 整数 (int または long) の式、または整数の配列を保持する動的な値。

* この関数は、最大64の引数を受け取ります。

## <a name="returns"></a>戻り値

この関数の引数によって指定されたコードポイント値を持つ Unicode 文字から成る文字列を返します。 入力は、有効な Unicode の方法で構成されている必要があります。
いずれかの引数が Unicode 文字にマップされていない場合、関数はを返し `null` ます。

## <a name="examples"></a>例

```kusto
print str = make_string(75, 117, 115, 116, 111)
```

|str|
|---|
|Kusto|

```kusto
print str = make_string(dynamic([75, 117, 115, 116, 111]))
```

|str|
|---|
|Kusto|

```kusto
print str = make_string(dynamic([75, 117, 115]), 116, 111)
```

|str|
|---|
|Kusto|

```kusto
print str = make_string(75, 10, 117, 10, 115, 10, 116, 10, 111)
```

|str|
|---|
|K<br>u<br>s<br>t<br>o|
