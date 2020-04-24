---
title: array_length() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでarray_length() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0bb1daeba6d24f8bd7326fcd0b8c17f06003e30b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518600"
---
# <a name="array_length"></a>array_length()

動的配列内の要素の数を計算します。

**構文**

`array_length(`*配列*`)`

**引数**

* *配列*:`dynamic`値。

**戻り値**

*array* 内の要素数。*array* が配列ではない場合は `null`。

**使用例**

```kusto
print array_length(parse_json('[1, 2, 3, "four"]')) == 4

print array_length(parse_json('[8]')) == 1

print array_length(parse_json('[{}]')) == 1

print array_length(parse_json('[]')) == 0

print array_length(parse_json('{}')) == null

print array_length(parse_json('21')) == null
```