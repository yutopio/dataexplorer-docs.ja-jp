---
title: to_utf8() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの to_utf8() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9d48ed99e517e0b1e5d498e80deaa48dc1cd3601
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505833"
---
# <a name="to_utf8"></a>to_utf8()

入力文字列の Unicode 文字の動的配列を返します (make_stringの逆の操作)。

**構文**

`to_utf8(`*ソース*`)`

**引数**

* *ソース*: 変換するソース文字列。

**戻り値**

この関数に指定された文字列を構成する Unicode 文字の動的配列を返します。
参照[`make_string()`](makestringfunction.md))

**使用例**

```kusto
print arr = to_utf8("⒦⒰⒮⒯⒪")
```

|Arr|
|---|
|[9382, 9392, 9390, 9391, 9386]|

```kusto
print arr = to_utf8("קוסטו - Kusto")
```

|Arr|
|---|
|[1511, 1493, 1505, 1496, 1493, 32, 45, 32, 75, 117, 115, 116, 111]|

```kusto
print str = make_string(to_utf8("Kusto"))
```

|str|
|---|
|Kusto|
