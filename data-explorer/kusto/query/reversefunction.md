---
title: リバース() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのリバース() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e95246e0586dff7dd89dc2658c7fae08b1bbaddf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510304"
---
# <a name="reverse"></a>reverse()

関数は入力文字列を逆にします。

入力値が文字列型でない場合、関数は強制的に値を文字列にキャストします。

**構文**

`reverse(`*ソース*`)`

**引数**

* *ソース*: 入力値。  

**戻り値**

文字列値の逆の順序。

**使用例**

```kusto
print str = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
| extend rstr = reverse(str)
```

|str|Rstr|
|---|---|
|ABCDEFGHIJKLMNOPQRSTUVWXYZ|ジクスヴヴルクポンムリックジグフェドCBA|


```kusto
print ['int'] = 12345, ['double'] = 123.45, 
['datetime'] = datetime(2017-10-15 12:00), ['timespan'] = 3h
| project rint = reverse(['int']), rdouble = reverse(['double']), 
rdatetime = reverse(['datetime']), rtimespan = reverse(['timespan'])
```

|rint|ダブル|時刻|タイムスパン|
|---|---|---|---|
|54321|54.321|Z0000000.00:00:21T51-01-7102|00:00:30|




 