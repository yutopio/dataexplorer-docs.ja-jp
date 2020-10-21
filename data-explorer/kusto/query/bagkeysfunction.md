---
title: bag_keys ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの bag_keys () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: f36022bb1e9d0f72f2f63e14be888c0f462ccc70
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245471"
---
# <a name="bag_keys"></a>bag_keys()

動的プロパティバッグオブジェクト内のすべてのルートキーを列挙します。

## <a name="syntax"></a>構文

`bag_keys(`*動的オブジェクト*`)`

## <a name="returns"></a>戻り値

キーの配列。順序は不定です。

## <a name="examples"></a>例

<!-- csl: https://help.kusto.windows.net/Samples -->
```
datatable(index:long, d:dynamic) [
1, dynamic({'a':'b', 'c':123}), 
2, dynamic({'a':'b', 'c':{'d':123}}),
3, dynamic({'a':'b', 'c':[{'d':123}]}),
4, dynamic(null),
5, dynamic({}),
6, dynamic('a'),
7, dynamic([])]
| extend keys = bag_keys(d)
```

|インデックス|d|キー|
|---|---|---|
|1|{<br>  "a": "b",<br>  "c": 123<br>}|[<br>  "a"、<br>  "c"<br>]|
|2|{<br>  "a": "b",<br>  "c": {<br>    "d": 123<br>  }<br>}|[<br>  "a"、<br>  "c"<br>]|
|3|{<br>  "a": "b",<br>  "c": [<br>    {<br>      "d": 123<br>    }<br>  ]<br>}|[<br>  "a"、<br>  "c"<br>]|
|4|||
|5|{}|[]|
|6|a||
|7|[]||
