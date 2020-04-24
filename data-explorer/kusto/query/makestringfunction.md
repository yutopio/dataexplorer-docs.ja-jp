---
title: make_string() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで make_string() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d5af7cab9106088064048c1077ec3f9b1950ec08
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512633"
---
# <a name="make_string"></a>make_string()

Unicode 文字によって生成された文字列を返します。
    
**構文**

`make_string (`*Arg1* [, *ArgN]..*`)`

**引数**

* *Arg1* ..*ArgN* : 整数 (整数または長整数) または整数の配列を保持する動的な値である式。

* この関数は、最大 64 個の引数を受け取ります。 

**戻り値**

この関数の引数によってコードポイント値が指定された Unicode 文字で構成される文字列を返します。 入力は、有効な Unicode コードポイントで構成されている必要があります。
引数が Unicode 文字にマップされていない場合、関数は null を返します。

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