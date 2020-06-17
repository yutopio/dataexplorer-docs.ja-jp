---
title: make_string ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの make_string () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 035be5910d173c75e585baa0b093dc6276bd4d63
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818595"
---
# <a name="make_string"></a>make_string()

Unicode 文字によって生成された文字列を返します。
    
**構文**

`make_string (`*Arg1*[, *ArgN*]...`)`

**引数**

* *Arg1* ...*ArgN*: 整数 (int または long) の式、または整数の配列を保持する動的な値。

* この関数は、最大64の引数を受け取ります。

**戻り値**

この関数の引数によって指定されたコードポイント値を持つ Unicode 文字から成る文字列を返します。 入力は、有効な Unicode の方法で構成されている必要があります。
いずれかの引数が Unicode 文字にマップされていない場合、関数はを返し `null` ます。

**使用例**

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
