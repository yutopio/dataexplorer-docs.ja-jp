---
title: 空() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの isempty() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2cb0e53aa16257398c20661c31494ca9dda17c1e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513619"
---
# <a name="isempty"></a>isempty()

引数`true`が空の文字列であるか、null の場合に返されます。
    
```kusto
isempty("") == true
```

**構文**

`isempty(`[*値*]`)`

**戻り値**

引数が空の文字列または null であるかどうかを示します。

|x|isempty(x)
|---|---
| "" | true
|"x" | False
|parsejson("")|true
|parsejson("[]")|False
|パースjson("{}")|False

**例**

```kusto
T
| where isempty(fieldName)
| count
```