---
title: bag_keys ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの bag_keys () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 45c4fec6e74bb8b25bfd83141fc2ee4110efa5e0
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85265044"
---
# <a name="bag_keys"></a>bag_keys()

動的プロパティバッグオブジェクト内のすべてのルートキーを列挙します。

`bag_keys(`*動的オブジェクト*`)`

**戻り値**

キーの配列。順序は不定です。

**使用例**

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

|インデックス (index)|d|キー|
|---|---|---|
|1|{<br>  "a": "b",<br>  "c": 123<br>}|[<br>  "a"、<br>  "c"<br>]|
|2|{<br>  "a": "b",<br>  "c": {<br>    "d": 123<br>  }<br>}|[<br>  "a"、<br>  "c"<br>]|
|3|{<br>  "a": "b",<br>  "c": [<br>    {<br>      "d": 123<br>    }<br>  ]<br>}|[<br>  "a"、<br>  "c"<br>]|
|4|||
|5|{}|[]|
|6|a||
|7|[]||
